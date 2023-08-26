- A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.
```bash
cd /etc/cron.d
ls
cat cronjob_bandit23
# same result as bandit 22 except it's 23
cd /usr/bin
cat cronjob_bandit23.sh
# returns a script that copies over a passwordfile to specified target
cd /tmp
./cronjob_bandit23.sh
# returns copying passwordfile /etc/bandit_pass/bandit23 to /tmp/lottacharacters
cd
cat /tmp/samecharactersfromresult
```
- QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G
- Debug code was very helpful
