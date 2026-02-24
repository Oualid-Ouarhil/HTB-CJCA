# Linux Firewall Setup (iptables) — CPTS/OSCP Notes

---

## 1. Overview

- Linux firewalls are built on the **Netfilter** framework (built into the kernel)
- **iptables** is the classic tool to configure Netfilter rules
- Alternatives:

|Tool|Notes|
|---|---|
|**iptables**|Classic, most widely used|
|**nftables**|Modern replacement, better performance, incompatible syntax|
|**ufw**|"Uncomplicated Firewall" — simple frontend for iptables|
|**firewalld**|Dynamic, zone-based, good for complex configs|

---

## 2. iptables Core Components

```
Tables → Chains → Rules → Matches + Targets
```

### Tables

|Table|Purpose|Built-in Chains|
|---|---|---|
|**filter**|Allow/block traffic|INPUT, OUTPUT, FORWARD|
|**nat**|Modify source/dest IPs|PREROUTING, POSTROUTING|
|**mangle**|Modify packet headers|PREROUTING, OUTPUT, INPUT, FORWARD, POSTROUTING|
|**raw**|Special packet processing|PREROUTING, OUTPUT|

### Chains

|Chain|Direction|
|---|---|
|**INPUT**|Incoming traffic destined for the local system|
|**OUTPUT**|Outgoing traffic from the local system|
|**FORWARD**|Traffic passing through (routing between interfaces)|
|**PREROUTING**|Before routing decision (modify dest IP)|
|**POSTROUTING**|After routing decision (modify source IP)|

> You can also create **user-defined chains** to group rules logically (e.g., all rules for port 80)

---

## 3. Targets (Actions)

|Target|Action|
|---|---|
|**ACCEPT**|Allow packet through|
|**DROP**|Silently block packet|
|**REJECT**|Block + send error back to source|
|**LOG**|Log to system log|
|**SNAT**|Change source IP (static NAT)|
|**DNAT**|Change destination IP (port forwarding)|
|**MASQUERADE**|Like SNAT but for dynamic IPs|
|**REDIRECT**|Redirect to different port/IP|
|**MARK**|Tag packet for advanced routing|

> **DROP vs REJECT:** DROP is stealthier (no response), REJECT tells the sender it was blocked. For pentesting, DROP on a target = harder to detect closed vs filtered.

---

## 4. Matches (Criteria)

|Match|Description|
|---|---|
|`-p / --protocol`|Protocol: `tcp`, `udp`, `icmp`|
|`--dport`|Destination port|
|`--sport`|Source port|
|`-s / --source`|Source IP|
|`-d / --destination`|Destination IP|
|`-m state`|Connection state: `NEW`, `ESTABLISHED`, `RELATED`|
|`-m multiport`|Multiple ports/ranges|
|`-m string`|Match packet containing a string|
|`-m limit`|Rate limiting|
|`-m conntrack`|Connection tracking info|
|`-m mac`|Match by MAC address|
|`-m iprange`|Match IP range|

---

## 5. Common Commands

bash

````bash
# View current rules
sudo iptables -L -v -n
sudo iptables -L -v -n --line-numbers   # with line numbers

# Allow incoming SSH (port 22)
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow incoming HTTP (port 80)
sudo iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT

# Block specific IP
sudo iptables -A INPUT -s 192.168.1.100 -j DROP

# Allow established/related connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Drop all other incoming traffic (default deny)
sudo iptables -A INPUT -j DROP

# Delete a rule by line number
sudo iptables -D INPUT 3

# Flush all rules (clear everything)
sudo iptables -F

# NAT / Port forwarding (DNAT)
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.1.10:80

# Masquerade (outbound NAT for dynamic IP)
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

---

## 6. Rule Syntax Breakdown
```
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
              |        |        |              |
              Append   Protocol Dest Port      Target/Action
              to INPUT
````

---

## 7. Pentest Relevance

- **Enumerate firewall rules** if you get shell access — `iptables -L -v -n`
- **DROP vs REJECT** matters in nmap scans:
    - `DROP` → port appears **filtered**
    - `REJECT` → port appears **closed**
- **DNAT rules** reveal internal services being forwarded — useful for pivot targets
- **MASQUERADE/SNAT** tells you the box is acting as a **NAT gateway** — potential pivot point
- **Flushing rules** (`iptables -F`) during post-exploitation can open up the network — but noisy