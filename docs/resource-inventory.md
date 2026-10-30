# Pre-Peering Network Resource Inventory

This document records the baseline network configuration for the Mumbai (`ap-south-1`) region before setting up VPC Peering.

## 1. Virtual Private Clouds (VPCs)
| VPC Name | IPv4 CIDR | AWS Resource ID | Description |
| --- | --- | --- | --- |
| ewb-alpha-vpc | 10.10.0.0/16 | vpc-00759f9a840911a3b | Primary VPC hosting public bastion and private subnet |
| ewb-beta-vpc | 10.20.0.0/16 | vpc-01548b7dc6bac9497 | Secondary isolated VPC hosting beta private subnet |

## 2. Subnets
| Subnet Name | Associated VPC | IPv4 CIDR | Availability Zone | AWS Resource ID |
| --- | --- | --- | --- | --- |
| ewb-alpha-public-subnet-a | ewb-alpha-vpc | 10.10.1.0/24 | ap-south-1a | subnet-0e7bec088c229e819 |
| ewb-alpha-private-subnet-a | ewb-alpha-vpc | 10.10.2.0/24 | ap-south-1a | subnet-0725e20854cf540ce |
| ewb-beta-private-subnet-a | ewb-beta-vpc | 10.20.1.0/24 | ap-south-1a | subnet-069e79b841cec8f2c |

## 3. Internet Gateways (IGW)
- **Gateway Name:** `ewb-alpha-igw` (ID: `igw-066f6dd563aabb764`)
- **Attachment:** Attached exclusively to `ewb-alpha-vpc`

## 4. Custom Route Tables
| Route Table Name | Associated Subnet | Active Routes | AWS Resource ID |
| --- | --- | --- | --- |
| ewb-alpha-public-rt | ewb-alpha-public-subnet-a | 10.10.0.0/16 (Local), 0.0.0.0/0 (Target: ewb-alpha-igw) | rtb-028f09d18ef9f16ac |
| ewb-alpha-private-rt | ewb-alpha-private-subnet-a | 10.10.0.0/16 (Local) | rtb-00edcc1de34daae0b |
| ewb-beta-private-rt | ewb-beta-private-subnet-a | 10.20.0.0/16 (Local) | rtb-07922750cf85257de |
