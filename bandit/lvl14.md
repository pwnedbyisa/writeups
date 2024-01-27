> Netcat <br>
> The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.
```bash
cd /etc/bandit_pass
cat bandit14
echo 'bandit14pass' | nc localhost 30000
```
