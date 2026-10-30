# Network Troubleshooting & Diagnostic Playbook

This document captures the systematic diagnostic procedures and failure analyses recorded during the AWS VPC Peering laboratory.

---

## 🧭 Systematic 9-Point Troubleshooting Order
When diagnosing a connectivity failure between instances in a peered VPC network, always execute diagnostics in this exact sequence to isolate the layer of failure:

1. **Check Peering Status:** Verify that the VPC peering connection status shows `active` in the AWS console.
2. **Check Subnet-to-Route Table Association:** Ensure each subnet is explicitly associated with its dedicated custom route table rather than defaulting to the main VPC route table.
3. **Check Peer Destination and Target:** Confirm that the route tables contain the correct target CIDR blocks pointing toward the `pcx-` connection.
4. **Check Beta Return Route:** Verify that the destination VPC (Beta) has a return path pointing back to the requester's IP block.
5. **Check Target Security Group:** Audit the destination's inbound rules to ensure the specific protocol (SSH/ICMP) and exact source IP/security group are authorized.
6. **Check Default Network ACL:** Verify that stateless Network Access Control Lists (NACLs) are not blocking ephemeral ports or return traffic.
7. **Check Target Private IPv4 Address:** Confirm that target commands are using the exact, active private IP address assigned to the destination EC2 instance.
8. **Check Instance Status Checks:** Ensure that both the source and target EC2 instances are in a running state and show `2/2 checks passed`.
9. **Check ProxyJump Host Information Last:** Verify the local Windows OpenSSH configuration file for hostname typos, correct SSH key paths, and dynamic IP changes.

---

## 🔎 Real-World Failure Analyses

### Case 1: The Missing-Route Baseline (Initial Setup)
- **Symptom:** App-A was completely unable to ping App-B, resulting in silent hangs and 100% packet loss.
- **Root Cause:** Although both VPCs were fully configured and had active instances, there was no logical bridge or route table entries connecting the separate IP blocks (`10.10.0.0/16` and `10.20.0.0/16`).
- **Resolution:** Established the VPC Peering connection (`ewb-alpha-beta-pcx`) and added explicit routes inside both VPC route tables targeting the peer.

### Case 2: The Active-Peering Route Limitation
- **Symptom:** Attempting to SSH or ping after activating the VPC Peering relationship still timed out.
- **Root Cause:** A common misconception is that activating a peering connection automatically routes traffic. Peering only authorizes the connection; without manual updates to the public and private subnet route tables, the routers do not know where to direct the packets.
- **Resolution:** Manually added routes inside `ewb-alpha-public-rt`, `ewb-alpha-private-rt` (targeting `10.20.0.0/16`), and `ewb-beta-private-rt` (targeting `10.10.0.0/16`).

### Case 3: The Controlled ICMP Security Failure
- **Symptom:** After established connection, pinging from App-A to App-B suddenly timed out, but the independent SSH administrative channel (ProxyJump) remained fully functional.
- **Root Cause:** Deleted the Custom ICMP IPv4 (Echo Request) inbound rule from App-B's security group (`ewb-app-b-sg`). Because security groups are stateful and operate at the instance level, they evaluated and silently dropped the ICMP ping packets before they could touch the server interface, while allowing SSH traffic over Port 22.
- **Resolution:** Re-added the inbound ICMP rule on App-B permitting Echo Requests from App-A's exact private IP address (`10.10.2.10/32`).

### Case 4: The Stale Peer Security-Group Reference
- **Symptom:** SSH or ICMP packets suddenly time out after deleting and rebuilding a network, even if security groups look identical.
- **Root Cause:** Deleting a VPC Peering connection immediately invalidates and "freezes" any cross-VPC security-group references. The rule on the security group will show up as stale or disabled because the logical bridge authorizing that membership lookup is gone.
- **Resolution:** Re-establish the active peering connection, delete the stale rule, and recreate it referencing the newly generated peer security-group ID.
