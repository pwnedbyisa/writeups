- There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).
- NOTE: Try connecting to your own network daemon to see if it works as you think
```bash
ls -la
echo <bandit20pass> | nc -l -p 1234 &
# & lets you work in the terminal while nc is running
./suconnect 1234
```
