This Terraform configuration deploys a secure, multi-VPC on AWS with Transit Gateway and a Bastion Host. This includes VPCs for production, development, and testing environments, facilitating inter-VPC communication and secure remote access.

This uses Transit Gateway to interconnect three VPCs—isolated within their own CIDR range. A Bastion Host in the Prod VPC provides secure SSH access to instances in private subnets, enhancing security while enabling maintenance and management tasks. This streamlines network configuration, reduces the complexity of managing multiple VPC peering connections, and supports scalable, isolated environments.

Key Components
VPCs: Each environment (Production, Development, Testing) is isolated within its own VPC.
Subnets: Public and private subnets within each VPC provide flexible networking options for different workloads.
Transit Gateway: Acts as a centralized hub to route traffic between VPCs.
Bastion Host: Located in the Production VPC’s public subnet for secure SSH access to instances in private subnets.
Route Tables: Control the routing between VPCs through the Transit Gateway.
Files and Configuration Details

00-vpc.tf - VPC and Subnet Configuration
This file defines the VPCs for Production, Development, and Testing environments, each with a unique CIDR range and both public and private subnets.
Production VPC: For hosting production resources in isolated private and public subnets.
Development VPC: For development resources that may require isolated access.
Testing VPC: For testing resources in a sandboxed environment.
Each VPC is isolated with its own CIDR block, ensuring network segmentation for security and compliance. When connected via Transit Gateway, the VPCs can communicate based on predefined routing rules, allowing controlled inter-VPC access.

01-bastion-host.tf - Bastion Host and Security Group Configuration
Defines a Bastion Host in the Production VPC for secure access to private resources across VPCs and a Security Group to control SSH access.
Provides SSH access to private resources within all VPCs via Transit Gateway.
Security Group: Restricts access to specific IPs for enhanced security.
The Bastion Host operates within the Production VPC’s public subnet, independently accessible only from whitelisted IPs. This allows secure access to private instances across VPCs through Transit Gateway, essential for monitoring, troubleshooting, and maintenance.

02-transit-gateway.tf - Transit Gateway and Route Tables
Creates the Transit Gateway and attaches each VPC to it, enabling centralized and secure routing between VPCs. Each VPC’s private subnet is configured with a route table entry to send inter-VPC traffic to the Transit Gateway.
Transit Gateway: Serves as a central hub for all VPC-to-VPC traffic.
Route Tables: Direct traffic from private subnets to the Transit Gateway for inter-VPC communication.
Allows centralized control of network traffic, enabling simplified routing configuration and management. Ensures private subnets in each VPC can communicate with each other via the Transit Gateway, supporting cross-environment workflows and data sharing.

03-output.tf - Outputs for Resource IDs
Provides output variables for easy reference to the Transit Gateway ID and the VPC attachments.
Output resource IDs for tracking and using the Transit Gateway and VPC attachment details.
Benefits to the Organization
Network Scalability: The Transit Gateway facilitates a scalable network topology by reducing the need for numerous VPC peering connections, which simplifies network management.
Enhanced Security: Each VPC’s isolated environment and secure access through the Bastion Host limit exposure of sensitive resources.
Operational Efficiency: Centralized routing through the Transit Gateway streamlines network traffic management across environments.
Flexibility for Workflows: The interconnection of Production, Development, and Testing environments supports cross-team collaboration and resource sharing while maintaining environment separation.


After deployment, the output variables will display the Transit Gateway ID and VPC attachment IDs.

Note: The Bastion Host security group is set to allow SSH access from any IP (0.0.0.0/0). One can adjust the CIDR block for the ingress rule to restrict access. An admin can replace key_name in 01-bastion-host.tf with your SSH key pair to access the Bastion Host. This provides a robust, multi-environment network setup, enabling streamlined access, secure connectivity, and effective environment management across AWS.
