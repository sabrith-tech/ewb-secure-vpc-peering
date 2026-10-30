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
## Test 2: Forward Peering Test (App-A to App-B)
- **Status:** Pass
- **Command:** `ping -c 4 10.20.1.10`
- **Output Observed:**
```bash
[ec2-user@ip-10-10-2-10 ~]$ ping -c 4 10.20.1.10
PING 10.20.1.10 (10.20.1.10) 56(84) bytes of data.
64 bytes from 10.20.1.10: icmp_seq=1 ttl=127 time=0.439 ms
64 bytes from 10.20.1.10: icmp_seq=2 ttl=127 time=0.192 ms
64 bytes from 10.20.1.10: icmp_seq=3 ttl=127 time=0.169 ms
64 bytes from 10.20.1.10: icmp_seq=4 ttl=127 time=0.226 ms

--- 10.20.1.10 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3124ms
rtt min/avg/max/mdev = 0.169/0.256/0.439/0.107 ms
```
