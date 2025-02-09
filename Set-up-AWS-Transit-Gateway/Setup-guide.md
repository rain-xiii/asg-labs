
## **Overview**

This guide provides an overview of setting up a **centralized network architecture** using **AWS Transit Gateway** and CloudFormation. The focus is on key concepts, configuration strategies, and considerations for integrating multiple VPCs into a scalable and manageable network topology.

---

## **Step 1: Deploy Network Infrastructure with CloudFormation**

1. **Create a Key Pair**
    
    - Generate a key pair to be used for SSH access.
    - Store the private key securely.

2. **Download the CloudFormation YAML Template**
    
    - This template provisions the necessary VPCs, subnets, and baseline infrastructure.
    - CloudFormation uses YAML/JSON templates to define and automate AWS resource creation.

3. **Deploy the CloudFormation Stack**
    
    - Upload the YAML file to AWS CloudFormation.
    - Provide the **Key Pair** created earlier during stack creation.
    - Review and launch the stack.

![Cloudformation stack](/Set-up-AWS-Transit-Gateway/screenshots/Cloudformation-stack-created.PNG)
4. **Verify the Infrastructure**
    
- Once the stack is created, navigate to the **Outputs** tab in CloudFormation.
- Check the assigned **IP addresses** for the EC2 instances.

![Cloudformation stack Outputs](/Set-up-AWS-Transit-Gateway/screenshots/Cloudformation-stack-Outputs.PNG)

5. **Copy Key Pair to Public EC2 Instances**
    
    - Transfer the private key to public EC2 instances to allow SSH access to private EC2 instances later.

---

## **Step 2: Create AWS Transit Gateway**

6. **Deploy the Transit Gateway**
    
    - Navigate to the **AWS VPC Console → Transit Gateways**.
    - Click **Create Transit Gateway**.
    - **Uncheck** the following options:
        - **Default route table association**
        - **Default route table propagation**
    - This provides better control over route configurations and prevents unintentional routing leaks.
7. **Create Transit Gateway Attachments for VPCs**
    
    - Attach each VPC to the Transit Gateway.
    - **What is an Attachment?**
        - An attachment is a **physical connection** between a VPC and the Transit Gateway.
        - Without an attachment, the Transit Gateway cannot communicate with the VPC.
        - The **Route Table** only defines the path, but the attachment provides the connection.
        - Transit Gateway operates at **Layer 3 (Network Layer)** but still requires an underlying **physical link (Layer 2)**.
    
    🔹 **Note:** Each VPC in this setup has **only one subnet attached**, but in a production environment, multiple subnets should be attached for redundancy.

![Transit gateway Attachment](/Set-up-AWS-Transit-Gateway/screenshots/Transit-gateway-attachment-created.PNG)
8. **Verify Connectivity** (Before Routing)
    
- SSH into a **Public EC2 instance**.
- Attempt to **ping** a private EC2 instance.
- The ping will **fail** because routing is not yet configured.

![Fail from ping to EC2 private](/Set-up-AWS-Transit-Gateway/screenshots/EC2-1-ping-to-EC2-2.PNG)
---

## **Step 3: Configure Transit Gateway Routing**

9. **Create and Configure Transit Gateway Route Tables**
    
- Create a **Transit Gateway Route Table**.
- **Associate** all four VPC attachments with the route table.

![All four VPC attachments associated](/Set-up-AWS-Transit-Gateway/screenshots/TGW-associations-created.PNG)

- **Propagate** routes for each attachment.

![All four attachments propagated](/Set-up-AWS-Transit-Gateway/screenshots/TGW-propagations-created.PNG)

10. **Update VPC Route Tables**
    
    - Navigate to **VPC Route Tables**.
    - **For Public Subnets:**
        - Add a route: **Destination: 172.16.0.0/16** → **Target: Transit Gateway**
        - This allows communication between VPCs within the designated IP range.
    - **For Private Subnets:**
        - Add a route: **Destination: 0.0.0.0/0** → **Target: Transit Gateway**
        - This setup ensures optimized private VPC connectivity while maintaining **centralized control** over inter-VPC routing.

---

## **Step 4: Test Connectivity**

11. **SSH into a Public EC2 Instance**
    
    - Connect to a **Public EC2 instance** using the key pair.
    - Use SSH Agent Forwarding to access Private EC2 instances.
12. **Ping Between Private EC2 Instances**
    
    - Once connected, **ping other EC2 instances** to confirm successful Transit Gateway routing.
    - If connectivity works as expected, the **Transit Gateway setup is complete!** 🎉

![Test success](/Set-up-AWS-Transit-Gateway/screenshots/Success-ping-from-EC2-2-to-3.PNG)
---

## **Key Considerations**

- **Security:**
    - Avoid opening **SSH or RDP to the internet**; use **AWS Systems Manager Session Manager** when possible.
    - Secure **route propagation** to prevent accidental exposure of sensitive network paths.
- **Scalability:**
    - AWS Transit Gateway supports thousands of VPC connections, reducing the need for complex **VPC peering**.
- **Monitoring:**
    - Use **VPC Flow Logs** and **AWS CloudWatch** to monitor network traffic and troubleshoot issues.

By implementing **AWS Transit Gateway**, organizations can achieve a **centralized, scalable, and secure** network architecture, simplifying VPC-to-VPC and hybrid cloud connectivity.