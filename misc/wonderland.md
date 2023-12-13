 This was a tryhackme CTF

 - I started with regular nmap enumaration and found http and ssh were running
 - Going to the website adn viewing source, I foud the img directory
 - Three images were in the directory, so I downloaded all three and ran steg checks with `https://futureboy.us/stegano/decinput.html`
 - The only one with info was the white rabbit picture, with resulted in this:
```
follow the r a b b i t
```
- I had previously run gobuster to enumerate subdirectories and found the directory `/r`
```
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
- 
