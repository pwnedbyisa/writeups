> OpenSSL <br>
> The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption. <br>
> Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…
```bash
echo '<bandit15pass>JQttfApK4SeyHwDlI9SXGR50qclOAil1' | openssl s_client -ign_eof localhost:30001
# password under read r block
```
