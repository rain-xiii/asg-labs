# VPN Site-to-Site  

This section showcases my ability to configure and troubleshoot AWS Site-to-Site VPN, a secure and scalable solution for connecting on-premises networks to AWS or interconnecting multiple VPCs.  

## 🌍 Overview  
This document provides an overview of the Site-to-Site VPN setup, explaining its architecture, key configurations, and solutions, including  
- Configuring **Customer Gateway** and **Virtual Private Gateway**  
- Setting up VPN Connections and Tunnels  
- Implementing **Route Propagation** in Route Tables  
- Security considerations and troubleshooting

## 📐 Architecture Diagram
![VPN Site to Site Architecture Diagram ](/VPN-Site-to-Site/screenshots/VPN-Architecture-Diagram.png) 
- **Components:**
  1. VPC:

     - Each network environment (one for AWS and one for the on-premise environment) is deployed in a separate VPC, ensuring network isolation and security.
      - AWS Environment: This VPC contains resources like EC2, RDS, and other AWS services.
     - On-premise Environment: A VPC simulating the customer’s on-premise environment, which will be connected to AWS via VPN.
  2. EC2:

      - Virtual servers running on EC2, simulating the servers within the customer’s environment or applications deployed in the system.
  3. Internet Gateway:

      - Provides internet access for the servers in the VPC. Servers can access the internet through this gateway.
  4. NAT Gateway:

      - Allows servers in private subnets (which don't have public IPs) to connect to the internet, for example, to download software updates, but prevents internet access to these servers.
  5. Virtual Private Gateway (VPN Gateway):

      - AWS's connection point that enables Site-to-Site VPN connections between the AWS VPC and the customer’s on-premise network. It’s a secure gateway that allows data to flow between the two environments over an encrypted connection.
  6. Customer Gateway:

      - A connection point on the customer’s side (on-premise) that enables the customer’s network to connect to the AWS VPC via the VPN connection. This is the starting point of the secure link between the customer and AWS.

## 🛠 Setup-guide
- Summary of the steps and explanation of the ideas [here](/VPN-Site-to-Site/Setup-guide.md).
## ⚠️ Troubleshooting
- Common VPN connection errors and their troubleshooting steps [here](/VPN-Site-to-Site/Troubleshooting.md).

## 🎯 Goals  
By working on this, I aim to:  
✅ Demonstrate a deep understanding of AWS networking & VPN configuration  
✅ Showcase hands-on experience with AWS Site-to-Site VPN  
✅ Improve troubleshooting skills in real-world scenarios  

## 🔗 External Resources
For more detailed instructions and reference material, you can refer to the original AWS Study Group Guide:

- [Start with Amazon VPC and AWS VPN Site-To-Site](https://000003.awsstudygroup.com/)