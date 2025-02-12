# Route 53 Resolver 

This section explores the implementation of a hybrid DNS architecture using Amazon Route 53 Resolver to enable seamless name resolution between AWS and on-premises environments. Instead of a step-by-step guide, the focus is on the core concepts, configuration strategies, and key considerations when integrating private and public DNS resolution across hybrid networks.

## 🌍 Overview  

Key topics covered:
- Understanding Route 53 Resolver Inbound and Outbound Endpoints
- Configuring Conditional Forwarding between AWS and on-premises networks
- Managing DNS resolution across VPCs and on-premises data centers

This setup ensures that resources in different environments can resolve domain names efficiently, improving connectivity and operational consistency in hybrid cloud architectures.

## 📐 Architecture Diagram

- Network infrastructure in AWS (emulate on-premises DNS system)
![On-premise-architecture](/Set-up-Hybrid-DNS/screenshots/On-premise-architecture.png)

- Hybrid DNS Resolution Flow with Route 53 Resolver
![Route 53 and AD flow](/Set-up-Hybrid-DNS/screenshots/Route-53-and-AD-flow.jpg)

## 🛠 Implementation-details

- Summary of the steps and explanation of the ideas [here](/Set-up-Hybrid-DNS/Implementation-details.md)

## 🎯 Goals  

The goal of this setup is to enable seamless and efficient DNS resolution between AWS VPCs and on-premises environments using Route 53 Resolver. By implementing this solution, you will:

- Establish bidirectional DNS resolution between AWS and on-premises networks.
- Configure inbound and outbound endpoints for Route 53 Resolver.
- Implement conditional forwarding to control DNS query routing.
- Ensure high availability for hybrid DNS infrastructure.
- Explore security considerations relevant to hybrid DNS configurations.

This setup is essential for hybrid cloud architectures, allowing services in AWS and on-premises environments to communicate using domain names rather than IP addresses, improving manageability and reducing operational complexity.

## 🔗 External Resources

For more detailed instructions and reference material, you can refer to the original **AWS Study Group** Guide:

- [Set up Hybrid DNS with Route 53 Resolver](https://000010.awsstudygroup.com/)