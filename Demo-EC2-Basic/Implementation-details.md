
## Overview

This document demonstrates key EC2 operations across two operating system environments—Windows and Linux. It illustrates my familiarity with essential EC2 features, including instance configuration, custom AMI creation, access recovery, and volume archiving.

## 1. Preparation

**Network Setup:**

- **Create VPCs:**
    - One VPC for Linux and one VPC for Windows.
- **Configure Security Groups:**
    - _Linux Security Group:_
        - SSH (port 22)
        - All ICMP-IPv4
    - _Windows Security Group:_
        - RDP (port 3389)
        - All ICMP-IPv4
- **Security Note:**  
    Setting the source to 0.0.0.0/0 can expose sensitive services (such as SSH, MySQL, and custom TCP) to the Internet. It is recommended to restrict access to trusted IP ranges or use additional security measures (e.g., VPN or AWS Systems Manager Session Manager).

## 2. Windows Instance Deployment and Custom AMI Preparation

- **Deploy a Windows Instance (2022 Base):**
    - Launch the instance and connect to it.
- **Configure for Imaging:**
    - In the EC2LaunchSettings, under “Prepare for imaging”, select **Shutdown without Sysprep**.
        - _Explanation:_  
            Choosing “Shutdown without Sysprep” means the instance is shut down without removing unique system data (such as SID, computer name, or driver configurations). This preserves the instance’s settings for a specific purpose.
        - _Caveat:_  
            Running Sysprep resets unique identifiers, which may lead to issues such as being unable to retrieve the Administrator password via the AWS Console. This becomes particularly problematic when managing multiple instances, as manually resetting key pairs for each is impractical.

## 3. EC2 Basic Operations

- **Instance Management:**
    - Change the EC2 Instance Type to demonstrate performance scaling.

    ![Changed the EC2 Instance Type](/Demo-EC2-Basic/screenshots/EC2-change-instance-type.png)

- **Snapshot and AMI Creation:**
    - Create an EC2 snapshot of the instance’s EBS volume.

    ![Created an EC2 snapshot](/Demo-EC2-Basic/screenshots/EC2-snapshot.png)

    - Build a custom AMI from the configured instance.
        - _Note:_ For Windows instances, the EC2 must be in a completely Stopped state to ensure that the Sysprep (if configured) is fully processed.
        - Also, note that AWS normally reboots the instance to ensure data integrity on attached EBS volumes. However, this reboot may result in downtime and a change in the public IP (if not using an Elastic IP), so we do not **Enable** the **Reboot** option to prevent these issues.

    ![Custom AMI](/Demo-EC2-Basic/screenshots/EC2-custom-AMI.png)

- **Launching from Custom AMI:**
    - Launch a new EC2 instance using the custom AMI and verify connectivity.

    ![Launch Custom AMI](/Demo-EC2-Basic/screenshots/EC2-launch-Custom-AMI.png)
## 4. Access Recovery Methods

- **For EC2 Windows:**
    - When the key pair is lost, use AWS Systems Manager (SSM) to regain access.
        - Ensure the SSM agent is active and that the instance has a role with the appropriate SSM permissions; in this demonstration, I assign the **SSMFullAccess** policy to the instance

        ![SSMFullAccess Role](/Demo-EC2-Basic/screenshots/Create-FullAccess-SSM-role-for-EC2.png)

        - Run the command “AWSSupport-RunEC2RescueForWindowsTool” through SSM.

        ![Run SSM Command](/Demo-EC2-Basic/screenshots/Run-command-in-SSM.png)

        - Retrieve the Administrator password from the Parameter Store.

        ![Retrive Password](/Demo-EC2-Basic/screenshots/Password-store-in-ParameterStore.png)

- **For EC2 Linux:**
    - When the key pair is lost, use User Data to update the authorized keys:
        - Create a new key pair (the private key is saved locally, while the public key is stored in AWS). Use PuTTYgen to generate the public key for this key pair.
        - Update the EC2 instance’s User Data (via **Actions** > **Instance Settings** > **Edit User Data**) with the new public key under “ssh-authorized-keys”.
			```
			Content-Type: multipart/mixed; boundary="//"
			
			MIME-Version: 1.0
			
			--//
			
			Content-Type: text/cloud-config; charset="us-ascii"
			
			MIME-Version: 1.0
			
			Content-Transfer-Encoding: 7bit
			
			Content-Disposition: attachment; filename="cloud-config.txt"
			
			#cloud-config
			
			cloud_final_modules:
			
			- [users-groups, once]
			
			users:
			
			  - name: ec2-user
			
				ssh-authorized-keys:
		
			```
        - Restart the instance to trigger cloud-init, which will update the `.ssh/authorized_keys` file accordingly.
        - _Warning:_ 
            - This solution only allows one opportunity to update the public key via EC2 user data. Copy the public key value from Puttygen carefully before starting the EC2 instance. 
                - By default, User Data runs only once when the instance is launched for the first time. If you want User Data to run again after restarting the EC2 instance, you must enable the "Run user data on every boot" option.
            - Stopping an instance that uses instance store volumes may result in data loss, so ensure proper backups beforehand.

![New key pair updated](/Demo-EC2-Basic/screenshots/New-keypair-update-on-EC2-Linux.png)

## 5. Archiving EBS Volumes

- **Benefits of Archive Tier:** Archive Tier can reduce storage costs by up to 75% compared to Standard Tier (e.g., $0.05/GB-month to $0.0125/GB-month in Singapore, Oct 2023). It’s ideal for infrequent data access, such as long-term backups, legacy AMIs, or compliance data. AMIs remain functional—you can deregister them, but snapshots stay in Archive Tier until manually deleted. 
- **Effective Deployment:** Choose restore options: Expedited Restore (2–5 mins) for urgent needs at a higher cost, Standard Restore (24–48 hrs) for most cases, and Bulk Restore (5–12 hrs) for large volumes. Automate this process with AWS Data Lifecycle Manager to move snapshots older than 90 days to Archive Tier. 
- **Caution:** Monitor orphan snapshots since they don’t automatically delete when deregistering AMIs. Use AWS Config or periodic scripts to delete them and avoid unnecessary charges.

![Archiving EBS Volumes](/Demo-EC2-Basic/screenshots/Snapshot-archive-processing.png)
   

## Summary 

This document outlines the essential steps I performed to demonstrate EC2's capabilities across diverse environments. It serves as a practical reference for my proficiency in managing EC2 instances and related AWS operations.

Feel free to explore the repository for additional details and supporting scripts.
