==`More`== and ==`Less`== are two commands known as pagers, they allow you to navigate big files forward or backwards without editing them.

```shell-session
TRIFAULT@htb[/htb]$ cat /etc/passwd | more
```

==`cat`== finds and open files after you specify the directory. The `/etc/passwd` file in Linux is like a phone directory for users on the system. It includes details such as the username, user ID, group ID, home directory, and the default shell they use.

redirected to more ==`more`== which automatically opens the pager at the start of the file. (escape the pager by clicking ==Q==) the output says in the terminal.


```shell-session
TRIFAULT@htb[/htb]$ less /etc/passwd
```

==`Less`== is almost the same as ==`More`== 

*More output:*
```shell-session
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
<SNIP>
--More--
```

*Less output:*
```shell-session
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
<SNIP>
:
```
 Less doesn't remain in the terminal.

==`Head`== Simply print first 10 lines of a file in case we are only interested in the beginning of the file. ==`Tail`== on the other hand is the opposite, it only prints the final 10 lines of a file.

```shell-session
TRIFAULT@htb[/htb]$ head /etc/passwd
```

```shell-session
TRIFAULT@htb[/htb]$ tail /etc/passwd
```

==`Sort`== results are rarely sorted, so we use this to sort it alphabetically or numerically to get a better view. 
```shell-session
TRIFAULT@htb[/htb]$ cat /etc/passwd | sort
```

==`Grep`== is a very powerful search tool, it looks for specific settings or conditions you set, as an example we can use it to find all users who have their default shell set to /bin/bash.
```shell-session
TRIFAULT@htb[/htb]$ cat /etc/passwd | grep "/bin/bash"

root:x:0:0:root:/root:/bin/bash
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
cry0l1t3:x:1001:1001::/home/cry0l1t3:/bin/bash
htb-student:x:1002:1002::/home/htb-student:/bin/bash
```
We can also use =="-V"== to make it exclude predefined patterns.
```shell-session
TRIFAULT@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin"

root:x:0:0:root:/root:/bin/bash
sync:x:4:65534:sync:/bin:/bin/sync
postgres:x:111:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
user6:x:1000:1000:,,,:/home/user6:/bin/bash
```

==`Cut`== is used to limit search even further for specific things between delimiters with (-d":"), anything between : will be shown and we can specify it further with field command (-f1,2) which tells it to extract first or second... column field.

```shell-session
TRIFAULT@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | cut -d":" -f1

root
sync
postgres
mrb3n
cry0l1t3
htb-student
```

==`Tr`== changes or translates certain characters, - You use **set1** and **set2** to specify what you want to change.
    
- Options like `-d` and `-s` control how characters are deleted or squeezed. 

```shell-session
TRIFAULT@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " "

root x 0 0 root /root /bin/bash
sync x 4 65534 sync /bin /bin/sync
postgres x 111 117 PostgreSQL administrator,,, /var/lib/postgresql /bin/bash
mrb3n x 1000 1000 mrb3n /home/mrb3n /bin/bash
cry0l1t3 x 1001 1001  /home/cry0l1t3 /bin/bash
htb-student x 1002 1002  /home/htb-student /bin/bash
```

=='Curl'== is a tool used to grab info about a running url, example =='curl'== www.facebook.com . we can also add other options to it like -o 'filename' to save the output into a file.

```
┌─[trifault@parrot]─[~]  
└──╼ $curl -I https://www.youtube.com -o Youtube.txt
```

**Show HTTP headers:**

`curl -I http://example.com`

**Send data with a POST request (like submitting a form):**

```
`curl -d "name=John&age=25" -X POST http://example.com/submit`
```

The `-d` flag is used to send data, and `-X POST` specifies that it's a POST request.

### Summary of Common `curl` Options:

- `-O` : Download a file and save with the same name.
    
- `-o [filename]` : Download and specify a filename.
    
- `-I` : Fetch headers only (HEAD request).
    
- `-L` : Follow redirects.
    
- `-X [method]` : Specify HTTP method (GET, POST, PUT, DELETE).
    
- `-d` : Send data with POST request.
    
- `-H` : Add a custom header (e.g., Content-Type).
    
- `-u [username:password]` : HTTP Basic Authentication.
    
- `-v` : Verbose mode (detailed request/response info).
    
- `-b` : Use cookies from a file.
    
- `-c` : Save cookies to a file.
    
- `-T` : Upload a file.
    
- `--max-time` : Set a timeout.


Linux controls file/directory access using **read (r), write (w), execute (x)** permissions set for **owner, group, and others**.

- **Directories:** `x` = enter/traverse, `r` = list contents, `w` = create/delete files
    
- Permissions use **octal numbers** (e.g., 754) and are changed with **chmod**
    
- Ownership is changed with **chown**
    
- **SUID/SGID:** programs run with owner/group privileges (security risk if misused)
    
- **Sticky bit:** in shared dirs, only file owners/root can delete files (`t` = executable, `T` = not executable)

//USER MANAGEMENT//

|**Command**|**Description**|
|---|---|
|`sudo`|Execute command as a different user.|
|`su`|The `su` utility requests appropriate user credentials via PAM and switches to that user ID (the default user is the superuser). A shell is then executed.|
|`useradd`|Creates a new user or update default new user information.|
|`userdel`|Deletes a user account and related files.|
|`usermod`|Modifies a user account.|
|`addgroup`|Adds a group to the system.|
|`delgroup`|Removes a group from the system.|
|`passwd`|Changes user password.|

//PACKAGE MANAGEMENT//

|**Command**|**Description**|
|---|---|
|`dpkg`|The `dpkg` is a tool to install, build, remove, and manage Debian packages. The primary and more user-friendly front-end for `dpkg` is aptitude.|
|`apt`|Apt provides a high-level command-line interface for the package management system.|
|`aptitude`|Aptitude is an alternative to apt and is a high-level interface to the package manager.|
|`snap`|Install, configure, refresh, and remove snap packages. Snaps enable the secure distribution of the latest apps and utilities for the cloud, servers, desktops, and the internet of things.|
|`gem`|Gem is the front-end to RubyGems, the standard package manager for Ruby.|
|`pip`|Pip is a Python package installer recommended for installing Python packages that are not available in the Debian archive. It can work with version control repositories (currently only Git, Mercurial, and Bazaar repositories), logs output extensively, and prevents partial installs by downloading all requirements before starting installation.|
|`git`|Git is a fast, scalable, distributed revision control system with an unusually rich command set that provides both high-level operations and full access to internals.|

# Network Services

## SSH

### Install SSH Server

`sudo apt install openssh-server -y`

**Use:** Installs the SSH service so others can connect to your system

### Check SSH Status

`systemctl status ssh`

**Use:** Verifies if SSH is running

### Connect to a Remote Host

`ssh username@IP_address`

**Use:** Securely log into another Linux machine

### SSH Configuration File

`/etc/ssh/sshd_config`

**Use:** Configure SSH options (ports, authentication, access rules)

---

## NFS

### Install NFS Server

`sudo apt install nfs-kernel-server -y`

**Use:** Enables file sharing over a network

### Check NFS Status

`systemctl status nfs-kernel-server`

**Use:** Confirms NFS service is active

### NFS Configuration File

`/etc/exports`

**Use:** Defines which directories are shared and permissions

### Example NFS Share

`/home/user/share hostname(rw,sync,no_root_squash)`

**Use:** Shares a folder with read/write access

### Mount an NFS Share

`mount IP:/shared/folder /local/mountpoint`

**Use:** Access a remote shared folder locally

---

## Web Servers

### Install Apache

`sudo apt install apache2 -y`

**Use:** Installs Apache web server

### Apache Main Directory

`/var/www/html`

**Use:** Default folder for hosting web files

### Apache Configuration File

`/etc/apache2/apache2.conf`

**Use:** Controls server behavior and permissions

### Install Python

`sudo apt install python3 -y`

**Use:** Required for Python web server

### Start Python Web Server

`python3 -m http.server`

**Use:** Hosts current directory on port 8000

### Host Specific Directory

`python3 -m http.server --directory /path/to/folder`

**Use:** Share a specific folder

### Use Custom Port

`python3 -m http.server 443`

**Use:** Run server on a different port

---

## VPN (OpenVPN)

### Install OpenVPN

`sudo apt install openvpn -y`

**Use:** Installs VPN client/server

### OpenVPN Configuration File

`/etc/openvpn/server.conf`

**Use:** Controls encryption, tunneling, routing

### Connect to VPN

`sudo openvpn --config file.ovpn`

**Use:** Connects to a secure internal network

---

## Quick Security Summary (Important!)

- ❌ FTP → **Insecure**
    
- ✅ SSH / VPN → **Encrypted & Secure**
    
- ⚠️ NFS with `no_root_squash` → **High risk**
    
- ⚠️ Web servers → **Common attack targets**


# //HTTP-SERVER//

You can start a http server on linux on a specific port using this command 
`http-server -p 8080`  make sure u have it installed first by using:
`sudo npm install --global http-server` 