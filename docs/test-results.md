## Test 1: Pre-Peering Baseline (App-A to App-B)
- **Status:** FAIL (Expected baseline behavior)
- **Command:** `ping -c 4 10.20.1.10`
- **Output Observed:**
```bash
[ec2-user@ip-10-10-2-10 ~]$ ping -c 4 10.20.1.10
PING 10.20.1.10 (10.20.1.10) 56(84) bytes of data.

--- 10.20.1.10 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3128ms
```
