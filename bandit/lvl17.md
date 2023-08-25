- There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new
```bash
diff passwords.new passowrds.old
# returned two lines
# < hsmthsmthsmth
# > gsmthsmthsmth
cat passwords.old | grep 'hsmthsmth'
cat passwords.new | grep 'hsmthsnth'
# returned the same line, that's the next password
```
