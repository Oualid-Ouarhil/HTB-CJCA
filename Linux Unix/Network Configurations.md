# Linux Network Configuration 

---

## 1. Network Interfaces

### Viewing Interfaces

```bash
ifconfig          # deprecated but still widely used
ip addr           # modern replacement
```

**Key info shown:** IP address, netmask, broadcast, MAC address, MTU, RX/TX packets

### Interface Management

```bash
# Bring interface up
sudo ifconfig eth0 up
sudo ip link set eth0 up

# Assign IP
sudo ifconfig eth0 192.168.1.2

# Set netmask
sudo ifconfig eth0 netmask 255.255.255.0

# Set default gateway
sudo route add default gw 192.168.1.1 eth0
```

### DNS Configuration

```bash
# Temporary (lost on reboot)
sudo vim /etc/resolv.conf
# Add:
nameserver 8.8.8.8
nameserver 8.8.4.4
```

### Persistent Network Config

```bash
sudo vim /etc/network/interfaces
```

```
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```

```bash
sudo systemctl restart networking
```

> ⚠️ `/etc/resolv.conf` can be overwritten by NetworkManager or systemd-resolved — use proper network config files for persistence.

---

## 2. Network Access Control (NAC)

|Model|Description|
|---|---|
|**DAC** (Discretionary)|Resource _owner_ sets permissions|
|**MAC** (Mandatory)|OS enforces permissions based on security labels/clearances — used in gov/military/healthcare|
|**RBAC** (Role-Based)|Permissions tied to _roles_, not individuals — scalable for large orgs|

---

## 3. Hardening Tools

### SELinux

- **MAC** system built into the Linux kernel
- Fine-grained control over every process and file
- Very secure but complex to configure

### AppArmor

- **MAC** system implemented as a Linux Security Module (LSM)
- Uses **application profiles** — easier to manage than SELinux
- Slightly less granular but more user-friendly

### TCP Wrappers

- Host-based **network-level** access control
- Restricts service access based on **client IP address**
- Simpler than SELinux/AppArmor — no kernel-level resource control

|Tool|Level|Complexity|Granularity|
|---|---|---|---|
|SELinux|Kernel|High|Very fine|
|AppArmor|LSM Module|Medium|Fine|
|TCP Wrappers|Network|Low|IP-only|

---

## 4. Monitoring Tools

|Tool|Purpose|
|---|---|
|`syslog` / `rsyslog`|System & network logging|
|`ss`|Socket statistics|
|`lsof`|List open files/connections|
|ELK Stack|Elasticsearch + Logstash + Kibana — traffic analysis|
|Wireshark / tshark / tcpdump|Packet capture & analysis|

> **Pentest use case:** Capturing credentials over unencrypted protocols (e.g., FTP) via traffic analysis.

---

## 5. Troubleshooting Tools

```bash
ping <host>                        # Test connectivity, measure RTT
traceroute <host>                  # Trace packet path, identify hops
netstat -a                         # Active connections & listening ports
tcpdump                            # Packet capture CLI
nmap                               # Port/service/vuln scanning
```

### Common Network Issues

- Misconfigured firewalls/routers
- Bad DNS settings (always check DNS first!)
- Damaged cables or hardware failure
- Network congestion
- Unpatched firmware/software

---

## Quick Reference Cheat Sheet

```bash
ip addr                            # Show all interfaces
ifconfig eth0 up                   # Enable interface
ifconfig eth0 192.168.1.2          # Assign IP
route add default gw 192.168.1.1   # Set gateway
ping 8.8.8.8                       # Connectivity check
traceroute google.com              # Path tracing
netstat -a                         # Active connections
cat /etc/resolv.conf               # Check DNS
cat /etc/network/interfaces        # Check persistent config
```

---
