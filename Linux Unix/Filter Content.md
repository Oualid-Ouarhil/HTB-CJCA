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

cu