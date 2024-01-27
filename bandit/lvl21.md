> Cronjobs <br>
>  A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.
```bash
ls -la
cd /etc/cron.d
ls
cat cronjob_bandit22
# returned: @reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null

cd /usr/bin
cat cronjob_bandit22.sh

# Results
'''
> cronjob_bandit22.sh vvv
> #! /bin/bash
> chmod 644 /tmp/lottacharactershereimnottypingallathat
> cat /etc/bandit_pass/bandit22 > /tmp/samecharactersasabove
'''

cd
cat /tmp/samecharactersasabove
```
