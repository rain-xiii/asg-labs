# VPC Peering

This section covers the implementation of VPC Peering to establish private connectivity between Virtual Private Clouds (VPCs) within AWS. Instead of a step-by-step guide, the focus is on key concepts, configuration strategies, and important considerations when enabling network communication across VPCs.

## 🌍 Overview  

- Key Topics Covered:
    - Understanding VPC Peering and its role in AWS networking
    - Setting up peering connections between VPCs in the same AWS accounts/regions
    - Configuring route tables and security groups to allow traffic between peered VPCs
    - Limitations and best practices for managing scalability, security, and performance
- By leveraging VPC Peering, resources in separate VPCs can securely communicate over AWS’s internal network, without requiring a VPN or Transit Gateway. This approach is cost-effective and offers low-latency connectivity while ensuring controlled and secure access between environments.

## 📐 Architecture Diagram

![VPC peering](/Set-up-VPC-Peering/screenshots/VPC-Peering-Architect.png)

## 🛠 Implementation-details

- Summary of the steps and explanation of the ideas [here](/Set-up-VPC-Peering/Implementation-details.md)
  

## 🎯 Goals 
 
- The goal of this setup is to establish secure and private communication between VPCs using VPC Peering. This enables instances in different VPCs to communicate without traversing the public internet, reducing latency, cost, and security risks.

- By the end of this setup, you will:
    - Create a VPC Peering Connection between two or more VPCs
    - Configure Route Tables to enable traffic flow between peered VPCs
    - Update Security Groups to allow cross-VPC communication
    - Verify connectivity between instances across VPCs

- This setup is ideal for workloads requiring inter-VPC communication, such as multi-tier applications, shared services architectures, or connecting VPCs across AWS accounts and regions.
  
## 🔗 External Resources

For more detailed instructions and reference material, you can refer to the original **AWS Study Group** Guide:

- [Set up VPC Peering](https://000019.awsstudygroup.com/)
