> While having a home-brewed Mezcal in your favourite tavern, one of the most wanted cyborgs in the galaxy entered the room, he wanted to exact revenge on you for putting him away back in your bounty hunting days. A intense melee involving hand-to-hand combat ensued. Once you pinned him down, you tried to backdoor his system and neutralise him but he managed to escape. You successfully extracted the firmware from his system and pulled off the inversion cable for his torsional magnesium drive capacitor, so he mustn't have gone far. Your mission is to identify the suspect's current location and apprehend him.
#### Steps
1. Unzip the file w assword `hackthebox` to get the `boot_lodaer` and `efs.bin` files
2. Basic enumeration with file and binwalk didn't reveal much, except that `boot_loader` follows the intel HEX format
```
└─$ file boot_loader 
boot_loader: ASCII text
                                                                                                                                                                   
└─$ file efs.bin    
efs.bin: data

└─$ binwalk boot_loader 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Intel HEX data, record type: data


└─$ binwalk efs.bin    

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------                                                                               
```
3. Checking out the instance, I ran this nmap scan `nmap -sV -Pn -p 45638 94.237.53.58 -v`
```
PORT      STATE SERVICE VERSION
45638/tcp open  unknown
```
4. ...lovely
5. Then I ran another on the top 1000 ports `nmap -sV -Pn 94.237.53.58 -v` which showed that SSH and DNS were running
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
53/tcp open  domain  dnsmasq 2.83
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
6. After some research, I found that dnsmasq is a lightweight DNS forwarder and DHCP server, and I also found that it can be vulnerable to DNS cache poisoning
7. Going back to the files I found [this site](http://www.dlwrr.com/electronics/tools/hexview/hexview.html) for viewing intel hex, which also had some info on the structure of the text
8. Intel HEX lines follow the format `:LLAAAATT<data>SS` (this [page](https://en.wikipedia.org/wiki/Intel_HEX) also has helpful info)
9. `LL` is numebr of data bytes, `AAAA` is the address, `TT` is the hex code (00 is data, 01 is EOF, etc), and `SS` is the checksum
10. While copying the file over I noticed that there were long periods where the data bytes did not change, and then they would change for a while and switch back and forth
11. 
