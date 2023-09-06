flag 0
- tbh I had no idea where to start, so I did my first checklist item for most web CTFs; add robots.txt to the end of the url to check for web crawlers
- the page showed `User-agent: *` and  `disallow /s3cr3t-ar3a`, along with the flag

flag 1
- my next step was to navigate to the forbidden filepath, which had a message saying the page was moved and peope who are allowed access know where to go
- you know what that means... directory busting time! (spoiler alert this was a waste of 8 minutes)
- unfortunately dirb turned up nothing so I decided to dig a bit more on the webpage
- I used inspect to dig through the HTML and quite easily found another flag in the `alertbox` id

flag 2
- in the same alertbox that contained flag 1, there was a `next-page` attribute that pointed to the filepath `/apps-home`
- this page contains a few differen buttons, including one for a secure login portal
- clicking on the secure login page created a popup that prompted you to start a challege to access the secret area
- SQL injection didn't work, but the error message was `invalid unsername`, which is a pretty unique reponse
- from here, I tried 'grinch' and 'admin', effectively gave up, and decided to bruteforce for a valid username using wfuzz
```bash
wfuzz -zfile,/usr/share/wfuzz/wordlist/general/common.txt --hs 'Invalid Username' -d 'username=FUZZ&password=grinch' https://66eb8eed35791300628d3d4f7b00bccf.ctf.hacker101.com/secure-login/
```
- that returned the username 'access'
