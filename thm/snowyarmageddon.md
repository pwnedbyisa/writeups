WIP
> AoC Side Quest Ctf
- All that talk about being stealthy made me a bitttt nervous 
#### Question 1
- Started off with a basic nmap scan `nmap -sV -p- 10.10.19.223 -v`
```shell
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
23/tcp    open  tcpwrapped
8080/tcp  open  http       Apache httpd 2.4.57 ((Debian))
50628/tcp open  unknown
```
- The website was a 403 page that said "access is strictly forbidden for non-elves"
- `tcpwrapped` means theres some host based network protocol at play and nmap can't futher identify the service
- More detailed scanning:
```shell
└─$ nmap -sV -sC -p 22,23,8080,50628 10.10.19.223
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-21 21:55 CST

PORT      STATE  SERVICE VERSION
22/tcp    open   ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 98f8ebe8ed571fc1777a177e20e9ce43 (RSA)
|   256 e35f3acf2d4da27f81712e984815b786 (ECDSA)
|_  256 6296ceee26a9800ff1c0d76d459fb4df (ED25519)
23/tcp    open   tcpwrapped
8080/tcp  open   http    Apache httpd 2.4.57 ((Debian))
|_http-server-header: Apache/2.4.57 (Debian)
|_http-title: TryHackMe | Access Forbidden - 403
50628/tcp open   unknown
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.0 302 Redirect
|     Server: Webs
|     Date: Wed Dec 31 19:19:22 1969
|     Pragma: no-cache
|     Cache-Control: no-cache
|     Content-Type: text/html
|     Location: http://NC-227WF-HD-720P:50628/default.asp
|     <html><head></head><body>
|     This document has moved to a new <a href="http://NC-227WF-HD-720P:50628/default.asp">location</a>.
|     Please update your documents to reflect the new location.
|     </body></html>
|   HTTPOptions: 
|     HTTP/1.1 400 Page not found
|     Server: Webs
|     Date: Wed Dec 31 19:19:22 1969
|     Pragma: no-cache
|     Cache-Control: no-cache
|     Content-Type: text/html
|     <html><head><title>Document Error: Page not found</title></head>
|     <body><h2>Access Error: Page not found</h2>
|     when trying to obtain <b>(null)</b><br><p>Bad request type</p></body></html>
|   Help, SSLSessionReq: 
|     HTTP/1.1 400 Page not found
|     Server: Webs
|     Date: Wed Dec 31 19:19:39 1969
|     Pragma: no-cache
|     Cache-Control: no-cache
|     Content-Type: text/html
|     <html><head><title>Document Error: Page not found</title></head>
|     <body><h2>Access Error: Page not found</h2>
|     when trying to obtain <b>(null)</b><br><p>Bad request type</p></body></html>
|   RTSPRequest: 
|     HTTP/1.1 400 Page not found
|     Server: Webs
|     Date: Wed Dec 31 19:19:23 1969
|     Pragma: no-cache
|     Cache-Control: no-cache
|     Content-Type: text/html
|     <html><head><title>Document Error: Page not found</title></head>
|     <body><h2>Access Error: Page not found</h2>
|_    when trying to obtain <b>(null)</b><br><p>Bad request type</p></body></html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port50628-TCP:V=7.93%I=7%D=2/21%Time=65D6C5DA%P=x86_64-pc-linux-gnu%r(G
SF:etRequest,192,"HTTP/1\.0\x20302\x20Redirect\r\nServer:\x20Webs\r\nDate:
SF:\x20Wed\x20Dec\x2031\x2019:19:22\x201969\r\nPragma:\x20no-cache\r\nCach
SF:e-Control:\x20no-cache\r\nContent-Type:\x20text/html\r\nLocation:\x20ht
SF:tp://NC-227WF-HD-720P:50628/default\.asp\r\n\r\n<html><head></head><bod
SF:y>\r\n\t\tThis\x20document\x20has\x20moved\x20to\x20a\x20new\x20<a\x20h
SF:ref=\"http://NC-227WF-HD-720P:50628/default\.asp\">location</a>\.\r\n\t
SF:\tPlease\x20update\x20your\x20documents\x20to\x20reflect\x20the\x20new\
SF:x20location\.\r\n\t\t</body></html>\r\n\r\n")%r(HTTPOptions,154,"HTTP/1
SF:\.1\x20400\x20Page\x20not\x20found\r\nServer:\x20Webs\r\nDate:\x20Wed\x
SF:20Dec\x2031\x2019:19:22\x201969\r\nPragma:\x20no-cache\r\nCache-Control
SF::\x20no-cache\r\nContent-Type:\x20text/html\r\n\r\n<html><head><title>D
SF:ocument\x20Error:\x20Page\x20not\x20found</title></head>\r\n\t\t<body><
SF:h2>Access\x20Error:\x20Page\x20not\x20found</h2>\r\n\t\twhen\x20trying\
SF:x20to\x20obtain\x20<b>\(null\)</b><br><p>Bad\x20request\x20type</p></bo
SF:dy></html>\r\n\r\n")%r(RTSPRequest,154,"HTTP/1\.1\x20400\x20Page\x20not
SF:\x20found\r\nServer:\x20Webs\r\nDate:\x20Wed\x20Dec\x2031\x2019:19:23\x
SF:201969\r\nPragma:\x20no-cache\r\nCache-Control:\x20no-cache\r\nContent-
SF:Type:\x20text/html\r\n\r\n<html><head><title>Document\x20Error:\x20Page
SF:\x20not\x20found</title></head>\r\n\t\t<body><h2>Access\x20Error:\x20Pa
SF:ge\x20not\x20found</h2>\r\n\t\twhen\x20trying\x20to\x20obtain\x20<b>\(n
SF:ull\)</b><br><p>Bad\x20request\x20type</p></body></html>\r\n\r\n")%r(He
SF:lp,154,"HTTP/1\.1\x20400\x20Page\x20not\x20found\r\nServer:\x20Webs\r\n
SF:Date:\x20Wed\x20Dec\x2031\x2019:19:39\x201969\r\nPragma:\x20no-cache\r\
SF:nCache-Control:\x20no-cache\r\nContent-Type:\x20text/html\r\n\r\n<html>
SF:<head><title>Document\x20Error:\x20Page\x20not\x20found</title></head>\
SF:r\n\t\t<body><h2>Access\x20Error:\x20Page\x20not\x20found</h2>\r\n\t\tw
SF:hen\x20trying\x20to\x20obtain\x20<b>\(null\)</b><br><p>Bad\x20request\x
SF:20type</p></body></html>\r\n\r\n")%r(SSLSessionReq,154,"HTTP/1\.1\x2040
SF:0\x20Page\x20not\x20found\r\nServer:\x20Webs\r\nDate:\x20Wed\x20Dec\x20
SF:31\x2019:19:39\x201969\r\nPragma:\x20no-cache\r\nCache-Control:\x20no-c
SF:ache\r\nContent-Type:\x20text/html\r\n\r\n<html><head><title>Document\x
SF:20Error:\x20Page\x20not\x20found</title></head>\r\n\t\t<body><h2>Access
SF:\x20Error:\x20Page\x20not\x20found</h2>\r\n\t\twhen\x20trying\x20to\x20
SF:obtain\x20<b>\(null\)</b><br><p>Bad\x20request\x20type</p></body></html
SF:>\r\n\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
- I didn't have any issues with closed endpoints, but I assume noisier scanning techniques would cause problems
- Navigating to the webpage for port `50628`, we are redirected to `http://10.10.19.223:50628/en/login.asp` which shows a trivision camera
![image](https://github.com/pwnedbyisa/writeups/assets/138353745/352a6782-9e67-43f2-b438-eb2e64648a0a)
- Pressing enter brings up a login prompt. Because of the nature of the challenge, I immediately dismissed bruteforcing as a strategy
- 
#### Question 2
