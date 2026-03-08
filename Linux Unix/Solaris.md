# Solaris 

---

## 1. What is Solaris?

- Unix-based OS developed by Sun Microsystems, now owned by **Oracle**
- **Proprietary** — closed source (unlike most Linux distros)
- Used in: banking, finance, government, large data centers, cloud/virtualization
- Known for: robustness, scalability, high availability, fault tolerance
- Notable feature: built-in hypervisor — **Oracle VM Server for SPARC**

---

## 2. Solaris vs Linux — Key Differences

|Category|Linux (Ubuntu)|Solaris|
|---|---|---|
|**Source**|Open source|Closed/proprietary|
|**Filesystem**|ZFS, ext4|ZFS + **SMF** (Service Management Facility)|
|**Package Manager**|APT (`apt-get`)|IPS or **pkgadd** (SPM)|
|**Privilege System**|`sudo`|**RBAC** (sudo supported since Solaris 11)|
|**Service Mgmt**|systemd|SMF|
|**Process tracing**|`strace`|`truss`|
|**Open files**|`lsof`|`pfiles`|
|**System info**|`uname -a`|`showrev -a`|
|**NFS sharing**|`/etc/exports`|`share` command + `/etc/dfs/dfstab`|

---

## 3. Command Comparisons (Exam Critical)

### System Info

bash

```bash
# Linux
uname -a

# Solaris
showrev -a        # more detailed: patch level, hardware provider, kernel version
```

### Package Management

bash

```bash
# Linux
sudo apt-get install apache2

# Solaris
pkgadd -d SUNWapchr    # no sudo needed (RBAC handles privileges)
```

### File Permissions

bash

```bash
# Both use chmod
chmod 700 filename

# Find SUID files - Linux
find / -perm 4000

# Find SUID files - Solaris (note the dash before 4000)
find / -perm -4000
```

### NFS Sharing

bash

```bash
# Solaris — share a directory
share -F nfs -o rw /export/home

# Mount NFS share (same on both)
mount -F nfs 10.129.15.122:/nfs_share /mnt/local

# Solaris NFS config file
cat /etc/dfs/dfstab
```

### Process → Open Files

bash

```bash
# Linux
sudo lsof -c apache2

# Solaris
pfiles `pgrep httpd`
```

### Syscall Tracing

bash

```bash
# Linux
sudo strace -p `pgrep apache2`

# Solaris
truss ls
```

> **truss vs strace:** truss can also trace **signals** sent to a process and **child process** syscalls — strace cannot.

---

## 4. Solaris Directory Structure

Same as Linux with a couple of additions:

|Directory|Description|
|---|---|
|`/kernel`|Kernel modules and kernel-related files|
|`/opt`|Optional/third-party software packages|
|`/export/home`|Common location for user home dirs (NFS shared)|
|`/etc/dfs/dfstab`|NFS share configuration|

---

## 5. Security Features

- **RBAC (Role-Based Access Control)** — built-in, granular privilege assignment without full sudo
- **Mandatory Access Controls** — similar to SELinux/AppArmor
- **IPS (Image Packaging System)** — advanced package + update management

---

## 6. Pentest Relevance

- If you land on a Solaris box, **commands differ** — muscle memory for Linux won't always work
- Check `/etc/dfs/dfstab` for **NFS shares** — potential data exposure
- `truss` can reveal **sensitive info** during syscall tracing (credentials, file paths)
- RBAC means **privilege escalation paths differ** from standard Linux sudo abuse
- `pfiles` + `pgrep` combo useful for **process enumeration**
- `showrev -a` gives detailed patch level — helps identify **unpatched vulnerabilities**

---

## Quick Reference Cheat Sheet

bash

```bash
showrev -a                        # System info
pkgadd -d <package>               # Install package
find / -perm -4000                # Find SUID files
share -F nfs -o rw /export/home   # Share via NFS
pfiles `pgrep <process>`          # Open files by process
truss <command>                   # Trace syscalls
cat /etc/dfs/dfstab               # NFS config
```