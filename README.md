# aws-ec2-serial-console-demo
Demo the capabilities with EC2 serial console on a Windows Server

Check out the AWS Documentation regarding EC2 Serial Console for Windows Instances --> [Windows Guide to EC2 Serial Console](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-serial-console.html)

## Steps
1. Provision an EC2 Server (Windows Server 2019)
```aws ec2 run-instances \
    --image-id ami-0d43d465e2051057f \
    --count 1 \
    --instance-type t2.micro \
    --key-name DemoVPC_Key_Pair \
    --security-group-ids sg-09216138490915abb \
    --subnet-id subnet-069a69e50bd1ebb23 \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value="EC2 Serial Console Demo - Windows"}]'```

2. Enable EC2 Serial Console at the Account + Region level.
`aws ec2 enable-serial-console-access --region us-east-1`

3. Confirm EC2 Serial Console has been enabled for the Account.
`aws ec2 get-serial-console-access-status --region us-east-1`
Expect to see the following...
```{
    "SerialConsoleAccessEnabled": true
}```

4. RDP into the Windows EC2 instance in order to [Enable SAC and the boot menu] (https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/troubleshooting-sac.html).  I've created a bat script (enable_SAC_script.bat) to help simplify applying these settings.  Download the script and run it from the Windows instance.  Note: The instance will reboot to apply the settings.  Make sure you have saved/closed anything runnign on the instance prior to running the script.

5.

