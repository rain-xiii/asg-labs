## ** Overview**  
This guide details the setup of a **Site-to-Site VPN** between an **AWS environment** and a **customer’s on-premises network**. The process involves:  
1. Setting up the AWS network.  
2. Setting up the customer’s on-premises network.  
3. Configuring the **Site-to-Site VPN connection** between both environments.  

## **1. AWS Network Setup**  

### **Step 1: Create a VPC**  
- Create a **VPC** with CIDR block `10.10.0.0/16` (as per the architecture diagram).

![ASG VPC](/VPN-Site-to-Site/screenshots/ASG-VPC-Created.PNG)
### **Step 2: Create Public and Private Subnets**  
- Create **four subnets**:  
  - **2 public subnets**  
  - **2 private subnets**  
  - Each in a different Availability Zone (AZ).

![ASG Subnet - 2 public, 2 private](/VPN-Site-to-Site/screenshots/ASG-Subnet-Created.PNG)
### **Step 3: Configure Public Subnets**  
- **Internet Connectivity:**  
  - Create an **Internet Gateway** and attach it to the VPC.  
  - Create a **Route Table** for public subnets, routing traffic to the Internet Gateway.  
  - Associate the Route Table with public subnets.  
- **Security Group for Public Subnets:**  
  - Allow **SSH (port 22)** and **ICMP (ping)**.  
- **Deploy a Public EC2 Instance:**  
  - SSH into the instance and test internet connectivity by pinging an external website.

![ASG EC2 Public](/VPN-Site-to-Site/screenshots/ASG-EC2-public-created.PNG)
### **Step 4: Configure Private Subnets**  
- **Elastic IP Allocation:**  
  - Allocate an **Elastic IP** manually to maintain better control over IP assignments.  
- **NAT Gateway Configuration:**  
  - Deploy a **NAT Gateway** in **Public Subnet 2** and associate the Elastic IP.  
  - Create a **Route Table** for private subnets, routing outbound traffic to the NAT Gateway.  
  - Associate the Route Table with private subnets.

![ASG NAT Gateway](/VPN-Site-to-Site/screenshots/ASG-NAT-Created.PNG)
- **Security Group for Private Subnets:**  
  - Allow **SSH access only from the Public Subnet’s Security Group**.  
  - Allow **ICMP from anywhere** (for network testing).  
- **Deploy a Private EC2 Instance:**  
  - SSH into the Public EC2 instance.  
  - Copy the **key-pair** to the Public EC2 instance.  
  - From the Public EC2 instance, SSH into the Private EC2 instance.  
  - Test internet connectivity by pinging an external website.
![ASG EC2 Private](/VPN-Site-to-Site/screenshots/ASG-EC2-private-created.PNG)

---

## **2. Customer Network Setup (Simulated VPN Environment)**  
To represent the **on-premises network**, set up another VPC in a similar way.  

### **Step 5: Create a Simulated Customer Network**  
- **Create a VPC** (simulating the customer’s on-premises network).  
- **Create a Public Subnet**.  
- **Create an Internet Gateway** and attach it to the VPC.  
- **Create a Route Table** for the public subnet and route traffic to the Internet Gateway.  
- **Configure Security Group:**  
  - Open **UDP port 500** for VPN connectivity.  
  - If the VPN is behind NAT, open **UDP port 4500** instead.
- **Deploy a Customer EC2 Instance:**  
  - SSH into the instance and test internet connectivity by pinging an external website.

![Customer Instance](/VPN-Site-to-Site/screenshots/Customer-EC2-Public-Created.PNG)
---

## **3. Configuring the Site-to-Site VPN Connection**  

### **Step 6: Create a Virtual Private Gateway (VPG) in AWS**  
- Attach the VPG to the **AWS VPC**.  

### **Step 7: Create a Customer Gateway (CGW)**  
- The **Customer Gateway** represents the customer’s VPN endpoint.  
- Enter the **public IP** of the on-premises network's VPN device.  

### **Step 8: Create a VPN Connection (Site-to-Site VPN)**  
- Select the **Virtual Private Gateway (VPG)** and **Customer Gateway (CGW)** created earlier.  
- Choose **Static Routing** and enter the **on-premises VPC CIDR block** (since EC2 does not support BGP by default).  

### **Step 9: Enable Route Propagation**  
- In the **AWS Route Table** for both **Public and Private subnets**, enable **Route Propagation** to automatically add VPN routes.  

---

## **4. Configuring the Customer Gateway**  

### **Step 10: Download and Apply VPN Configuration**  
- Go to **VPN Connections** → Select your VPN → Click **Download Configuration** (choose OpenSwan vendor).  
- Follow the instructions to configure the on-premises VPN device.  

### **Step 11: Verify Connectivity**  
- In AWS, go to **VPN Connections** and check the **Tunnel Status**:  
  - At least **one tunnel should be “Up”**.

![VPN Connection Tunnel](/VPN-Site-to-Site/screenshots/VPN-connection-tunnel.PNG)  
- **Test connectivity:**  
  - From the **customer’s on-premises network**, ping the **private IP of the AWS Private EC2 instance**.
  ![Customer Instance ping to EC2 Private](/VPN-Site-to-Site/screenshots/Customer-instance-ping-to-EC2-private.PNG)  
  - From the **AWS Private EC2 instance**, ping the **customer’s on-premises network**.
  ![EC2 private ping to Customer Instance](/VPN-Site-to-Site/screenshots/EC2-private-ping-to-Customer-Instance.PNG)  

---

## **Summary **  
- We have successfully established a **Site-to-Site VPN** between AWS and a simulated customer network.  
- This setup enables **secure communication** between both environments.  
 

