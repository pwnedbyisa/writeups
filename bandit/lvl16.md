> More SSL <br>
> The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.
```bash
nmap -p 31000-32000 localhost
# returned 5 ports, guessed with each one (could be more efficient but oh well)

cd /etc/bandit_pass

echo '<bandit15pass>' | openssl s_client -ign_eof localhost:31790
# returns RSA priv key
# create /b17 folder in the /tmp directory
# echo the full key (including header and footer) into a file called bandit17.key

chmod 400 bandit17.key
# key is now executable

ssh bandit17@bandit.labs.overthewire.org -p 2220 -i bandit17.key
```
- Biggest issue for me was using the RSA key, make sure to include header and footer as well as the key
- Permission denied if the file is not private - chmod
