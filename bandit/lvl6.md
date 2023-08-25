- find
- The password for the next level is stored somewhere on the server and has all of the following properties: owned by user bandit7, owned by group bandit6, 33 bytes in size
```bash
ls -la
cd ..
find / -user bandit7 -group bandit6
# pulled /var/lib/dpkg/info/bandit7.password
cat var/lib/dpkg/info/bandit7.password
```
