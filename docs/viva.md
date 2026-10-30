# Technical Viva & Architectural Q&A

This document provides concise, high-impact responses to core architectural and networking questions based on the AWS VPC Peering project.

---

### Q1: Why must VPC Peering connections use non-overlapping CIDR blocks?
**Answer:** 
VPC Peering routes traffic privately using internal IP schemes. If two VPCs used overlapping CIDR blocks (for example, both using `10.10.0.0/16`), the router would experience IP address conflicts. It would be unable to determine whether a packet destined for a `10.10.x.x` address should stay local or be forwarded across the peering connection, leading to a complete routing failure. Non-overlapping IP allocations (such as `10.10.0.0/16` and `10.20.0.0/16`) ensure clear, unambiguous routing paths.

### Q2: Why are explicit route table updates required after activating a VPC Peering connection?
**Answer:** 
AWS separates the *authorization* layer of networking from the *data-routing* layer. Activating a VPC Peering connection only establishes a logical, authorized bridge between two VPCs. However, routers inside the subnets still strictly follow their assigned route tables. Without an explicit destination entry (e.g., sending `10.20.0.0/16` traffic to target `pcx-xxxxxx`), the subnets remain completely unaware of the new path and will send peer-bound traffic to default or local routes instead.

### Q3: What is a return path in VPC Peering and why is it mandatory?
**Answer:** 
Network communication is inherently two-way. When App-A sends a ping request to App-B, the packet reaches App-B successfully because of the forward route. However, for the ping to succeed, App-B must send a "ping reply" back to App-A. If the target VPC's route tables do not contain a return route pointing back to the requester's IP block (`10.10.0.0/16`), App-B's router will not know how to send the reply back, causing the request to time out. Both directions must have explicit route entries.

### Q4: How does SSH ProxyJump allow secure administration of private instances?
**Answer:** 
SSH ProxyJump allows a local client to establish a multi-hop secure tunnel. The client first connects and authenticates to a public-facing gateway (the Bastion Host) using an SSH key. Rather than logging directly into the bastion shell and risking private key storage on the gateway, the local SSH client tunnels a secondary SSH session directly through the bastion to the private instance (`App-A` or `App-B`). The private keys stay secure on the local Windows machine, and private instances remain completely closed to the public internet.

### Q5: What does it mean that AWS Security Groups are "stateful"?
**Answer:** 
Stateful firewalls track the state of active network connections. If you authorize an inbound rule (such as SSH on Port 22), the security group automatically remembers this connection and permits the outbound return traffic to pass back to the client, even if there are no outbound rules defined. This is the opposite of stateless firewalls (like Network ACLs), which require explicit rules for both inbound request traffic and outbound return traffic.

### Q6: What is VPC Peering non-transitivity?
**Answer:** 
VPC Peering does not support transitive routing. If VPC A is peered with VPC B, and VPC B is peered with VPC C, VPC A *cannot* communicate with VPC C through VPC B. To allow communication between A and C, you must establish a direct, explicit peering connection between VPC A and VPC C, or utilize a hub-and-spoke solution like AWS Transit Gateway.

### Q7: Why should we select "Standard" credits instead of "Unlimited" for T3 instances on the Free Tier?
**Answer:** 
By default, T3 instance families can burst above their baseline performance by spending CPU credits. Under "Unlimited" mode, if the instance exhausts its accumulated credits, AWS will automatically charge a premium to allow continued high-CPU burst performance. Selecting "Standard" mode prevents these automated charges by capping performance once the credit balance reaches zero, ensuring your sandbox remains 100% free.

### Q8: How could we automate this infrastructure in the future?
**Answer:** 
To scale this secure architecture, we can leverage Infrastructure as Code (IaC) tools like **Terraform** or **AWS CloudFormation**. Using declarative code, we can define our VPCs, subnets, route tables, peering connections, security groups, and EC2 instances. This eliminates manual configuration errors, guarantees exact replication, and allows the entire multi-VPC architecture to be deployed, updated, or torn down in a single command.
