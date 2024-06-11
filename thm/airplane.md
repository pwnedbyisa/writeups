> Are you ready to fly?
#### Steps
1. Started out with an nmap scan `nmap -sV 10.10.101.33 -v`
2. Got the ports `22` and `8000`, ssh and http
3. Navigating to `10.10.101.33:8000`, I got redirected to `airplane.thm`
4. I added this to my host file for DNS resolution with:
```shell
sudo sh -c "echo '10.10.101.33 airplane.thm' >> /etc/hosts"
```
5. Navigating to the page, I found the page file specified with a page parameter -> `http://airplane.thm:8000/?page=index.html`
6. After checking source  to make sure I didn't miss anything else, I modified the file path to grab `/etc/passwd`
```
http://airplane.thm:8000/?page=/../../../../etc/passwd
```
7. Easy LFI with no filters <333
8. From the file I found two users:
```
carlos:x:1000:1000:carlos,,,:/home/carlos:/bin/bash
hudson:x:1001:1001::/home/hudson:/bin/bash
```
9. I tried to use the same path traversal to read potential `user.txt` files in the home folders, but all I could find was a `.bashrc` file under hudsons directory
10. One interesting this is the `carlos,,,` section of carlos's line the in the /etc/passwd file
11. This is the `GECOS` section, and the following commas just mean that phone number, email, etc parameters are empty
12. So relatively useless </3
13. I tried running `hydra` to bruteforce `ssh` with the new usernames, but I didn't have much luck
14. Going back to the site itself, I decided to run a scan on all ports `-p-` to see if there were any other processes I missed
15. 
