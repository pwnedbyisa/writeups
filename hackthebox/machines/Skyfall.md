WIP
> Got stuck on an easy challenge so I decided to do an insane one instead <3
#### Steps
1. I started out with a basic nmap scan, finding `http` and `ssh` running
```
└─$ nmap -sV -p- 10.10.11.254 -v
[ ... ]
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
```
2. First I navigated to the webpage, which didn't have anything of interest in the source code, storage, links, etc.
3. I did find it interesting that the website itself and `https://www.google.com` both showed up under hosts for cookie data (though neither had any cookies)
4. Gave the IP a hostname to make the process easier
```
─$ sudo sh -c "echo '10.10.11.254 skyfall.htb' >> /etc/hosts"
```
5. Next I searched for directories
```
└─$ gobuster dir -u http://skyfall.htb -w /usr/share/dirb/wordlists/common.txt
```
6. `/assets/` and `index.html` were the results. Both could be found in the source code of the site and assets threw a 403 forbidden error </3
7. Next I looked for subdomains 
```
└─$ gobuster dns -d skyfall.htb -w /usr/share/wordlists/Subdomain.txt 
```
8. 
9. Doing some outside research, I found that there is a RCE memory overwrite vulnerability on nginx 1.18.0 called `CVE-2021-23017`
10. 
