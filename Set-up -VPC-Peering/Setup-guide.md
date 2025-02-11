# **Setup Guide: VPC Peering in AWS**

This guide provides a step-by-step approach to setting up **VPC Peering** between two VPCs using **AWS CloudFormation** and configuring networking components to enable secure communication.

---

## **1. Deploy VPCs and Networking Components**

### **Create VPCs**

- Deploy **My VPC** and **HG VPC** based on the defined CIDR blocks in the architecture diagram.

### **Configure Security Groups**

- Create a **Security Group** for **My VPC** and **HG VPC** that allows **SSH (port 22)** and **ICMP (ping)** for instances in the **Public Subnets**.

### **Update Network ACLs**

- Modify **Network ACLs** to restrict traffic, ensuring that only **My VPC's IP range** can access **HG VPC**.

---

## **2. Validate Initial Connectivity**

### **SSH and Ping Test**

1. **SSH into My VPC's EC2 Instance**
2. **Attempt to ping** an EC2 instance in HG VPC
    - This should **fail** because:
        - The instances previously communicated via **public IPs** through the **Internet Gateway**.
        - There is **no direct private route** between My VPC and HG VPC.

![Fail to ping to HG VPC](/VPC-Peering/screenshots/Fail-to-ping-HG-VPC.PNG)

---

## **3. Establish VPC Peering**

### **Create a VPC Peering Connection**

1. Initiate a **VPC Peering Request** from My VPC to HG VPC.
2. Accept the **Peering Request** in the AWS Console.

### **Test Connectivity Again**

- Try **pinging** the HG VPC instance from My VPC.
- This should still **fail** because the **Route Tables have not been updated**.

---

## **4. Configure Route Tables for Peering**

### **Update Route Tables in Both VPCs**

1. Add a **route in My VPC** to send traffic to **HG VPC’s CIDR** via the **VPC Peering Connection**.
2. Add a **route in HG VPC** to send traffic to **My VPC’s CIDR** via the **VPC Peering Connection**.

### **Test Connectivity Again**

- Ping the **HG VPC instance from My VPC**.
- The connection should now succeed.

---

## **5. Enable Cross-VPC DNS Resolution**

### **Modify Peering Connection Settings**

1. **Enable DNS Hostnames & DNS Resolution** for both VPCs.
2. **Modify the Peering Connection settings** to enable **DNS resolution across VPCs**.

![Enable Cross-Peering DNS](/VPC-Peering/screenshots/Enable-Cross-Peering-DNS.PNG)

### **Test DNS Resolution**

- SSH into My VPC’s EC2 instance and resolve the **Public DNS of HG VPC’s EC2 instance**.
- If **DNS Peering is not enabled**, the query will return the **Public IP**, forcing traffic through the **Internet Gateway**.
- If **DNS Peering is enabled**, it should return the **Private IP**, allowing private connectivity.

![Succeed ping to HG VPC](/VPC-Peering/screenshots/Succeed-to-ping-HG-VPC.PNG)

---

## **Final Verification**

- VPCs are securely peered.
- Route Tables are updated for **private communication**.
- Cross-VPC **DNS resolution is enabled**.
- EC2 instances in both VPCs can communicate **without using public IPs**.

This setup ensures **efficient, secure, and cost-effective** VPC communication, ideal for **multi-tier applications, microservices, or hybrid architectures**.