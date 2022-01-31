# aws-ec2-serial-console-demo
Demo the capabilities with EC2 serial console on a Windows Server

Check out the AWS Documentation regarding EC2 Serial Console for Windows Instances --> [Windows Guide to EC2 Serial Console](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-serial-console.html)

## Steps
1. Provision an EC2 Server (Windows Server 2019)
```
aws ec2 run-instances \
    --image-id ami-0d43d465e2051057f \
    --count 1 \
    --instance-type t3.xlarge \
    --key-name DemoVPC_Key_Pair \
    --security-group-ids sg-09216138490915abb \
    --subnet-id subnet-069a69e50bd1ebb23 \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value="EC2 Serial Console Demo - Windows"}]'
```

2. Enable EC2 Serial Console at the Account + Region level.
```
aws ec2 enable-serial-console-access --region us-east-1
```

3. Confirm EC2 Serial Console has been enabled for the Account.
```
aws ec2 get-serial-console-access-status --region us-east-1
```

Expect to see the following...
```
{
    "SerialConsoleAccessEnabled": true
}
```

4. RDP into the Windows EC2 instance in order to [Enable Special Admin Console (SAC) and the boot menu](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/troubleshooting-sac.html).  I've created a bat script (enable_SAC_script.bat) to help simplify applying these settings.  Download the script and run it from the Windows instance.  Note: The instance will reboot to apply the settings.  Make sure you have saved/closed anything runnign on the instance prior to running the script.

5. You can now navigate to the EC2 instance and connect via the "EC2 Serial Console".  Type `help` or `?` to get a listing of available SAC commands.  For example you can type `cmd` to create a Command Prompt channel, you can then switch to that channel, authenticate and interact with the OS via the Windows command prompt.


## Cleanup
1. Identify the EC2 Instance that was created.  Set it to a system variable for access in step #2
```
DEMO_EC2_INSTANCE=$(aws ec2 describe-instances --filters "Name=tag:Name,Values='EC2 Serial Console Demo - Windows'" | jq -r '.Reservations[0].Instances[0].InstanceId')
```

2. Terminate the EC2 Instance.
```
aws ec2 terminate-instances --instance-ids ${DEMO_EC2_INSTANCE}
```

3. Disable EC2 Serial Console in us-east-1.
```
aws ec2 disable-serial-console-access --region us-east-1
```


## Things to note:
* Only allowed one serial connection per EC2 instance at any one given time.  Close out previous connections to enable a new one.  If simply closing your browser tab, it may take a few minutes (likely only seconds) to close out the connection.
* Only EC2's leveraging the AWS Nitro System will support EC2 Serial Console.  Here is the link to Nitro supported instance types https://docs.aws.amazon.com/en_us/console/ec2//instances/nitro
* Link on how to "Use SAC" can be found here --> https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/troubleshooting-sac.html
* For Linux OS you will need to change the `PasswordAuthentication` in your sshd_config

