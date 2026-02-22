# Remote Desktop Protocols in Linux

---

## 1. Overview

|Protocol|Primary OS|Transport|Default Port|
|---|---|---|---|
|**RDP**|Windows|TCP|3389|
|**VNC**|Linux (cross-platform)|TCP|5900+|
|**X11**|Unix/Linux|TCP / Unix sockets|6000-6009|
|**XDMCP**|Unix/Linux|UDP|177|

---

## 2. X11 (X Window System)

- The graphical display system used in Linux/Unix
- **Network transparent** — apps can run on a remote machine but render locally (saves bandwidth and remote CPU)
- Transport: TCP/IP or Unix sockets
- Ports: **TCP 6000–6009** (display `:0` = port `6000`, `:1` = `6001`, etc.)

### X11 Forwarding over SSH

Enable in `/etc/ssh/sshd_config`:

```
X11Forwarding yes
```

Connect and launch a GUI app remotely:

bash

```bash
ssh -X htb-student@10.129.23.11 /usr/bin/firefox
```

### ⚠️ X11 Security Risks (Pentest Relevance)

- **Unencrypted by default** — traffic sent in cleartext
- **Always check ports 6000–6010** on Linux targets
- Attacker on the same network can:
    - Read window contents without sniffing (using `xwd`, `xgrabsc`)
    - Intercept passwords/sensitive data from X sessions
- **CVEs to know:** CVE-2017-2624, CVE-2017-2625, CVE-2017-2626 (XOrg Server — weak session keys → arbitrary code execution)
- **Mitigation:** Tunnel X11 over SSH

---

## 3. XDMCP

- Used by X Display Manager to manage remote X sessions
- Runs over **UDP port 177**
- **Insecure** — should never be used in secure environments
- Can redirect full GUIs (KDE, GNOME) to a remote client
- **Attack vector:** Man-in-the-middle — attacker intercepts session between X terminal and server → arbitrary command execution, data access

---

## 4. VNC (Virtual Network Computing)

- Based on the **RFB protocol**
- Cross-platform, commonly used for Linux remote graphical access
- Uses encryption + authentication (generally considered secure)
- **Default port: TCP 5900** (display `:0`)
- Additional sessions: `5901` (`:1`), `5902` (`:2`), etc.

### Common VNC Tools

|Tool|Notes|
|---|---|
|TigerVNC|Common, open source|
|TightVNC|Lightweight|
|**RealVNC**|Good encryption, widely used|
|**UltraVNC**|Good encryption, widely used|

### TigerVNC Setup (XFCE4)

bash

```bash
# Install
sudo apt install xfce4 xfce4-goodies tigervnc-standalone-server -y

# Set password
vncpasswd

# Create config files
touch ~/.vnc/xstartup ~/.vnc/config
chmod +x ~/.vnc/xstartup

# Start server
vncserver

# List sessions
vncserver -list
```

Config files:

bash

```bash
# ~/.vnc/xstartup
#!/bin/bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
/usr/bin/startxfce4

# ~/.vnc/config
geometry=1920x1080
dpi=96
```

### Secure VNC via SSH Tunnel

bash

```bash
# Create SSH tunnel (local port 5901 → remote 5901)
ssh -L 5901:127.0.0.1:5901 -N -f -l htb-student 10.129.14.130

# Connect through tunnel
xtightvncviewer localhost:5901
```

> `-L` = local port forward | `-N` = no command | `-f` = background

---

## 5. Pentest Cheat Sheet

bash

```bash
# Scan for X11 ports
nmap -p 6000-6010 <target>

# Scan for VNC
nmap -p 5900-5910 <target>

# Scan for XDMCP
nmap -sU -p 177 <target>

# X11 forward over SSH
ssh -X user@target <app>

# VNC tunnel + connect
ssh -L 5901:127.0.0.1:5901 -N -f -l user <target>
xtightvncviewer localhost:5901
```

---

## Key Takeaways for Exam

- X11 open on a target = potential **credential/screen interception** without sniffing
- XDMCP on UDP 177 = **MitM opportunity**, full GUI hijack possible
- VNC ports `590x` = always probe these on Linux targets
- Always tunnel VNC over SSH in real engagements
- GNOME + VNC = unstable; **XFCE4 is preferred** for VNC sessions