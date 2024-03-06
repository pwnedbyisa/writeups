#### Flag 1
- I stared with a basic nmap scan ont he top 1000 ports `nmap -sV IP -v`
- That revealed http and ssh, so I went to the website
- There was nothing interesting in the source code or pages I could find from source alone, so I used gobuster to enumerate
```
└─$ gobuster dir -u 10.10.51.244 -w /usr/share/dirb/wordlists/common.txt
```
- That brought me to the page `/robots.txt`
```
User-agent: *
Disallow: /zYdHuAKjP
```
- Navigating to the path, the page had an `access denied` message
- This usually indicates somethign to do with user agents or cookies, so I checked cookies first with inspect
- There was an `access` token with the value `denied` so I just changed it to `granted` and reloaded the page
- The access page had this string
```
hEzAdCfHzA::hEzAdCfHzAhAiJzAeIaDjBcBhHgAzAfHfN
```
> Hint: You're going to want to write a Python script for this. 'zA' = 'a'
- From this I assumed that every two letters map to one letter, and some type of substitution cipher was at play
- If every two letters maps to one letter, then the username has to be 5 characters long
```
hE zA dC fH zA :: hE zA dC fH zA hA iJ zA eI aD jB cB hH gA zA fH fN
```
- In the `/operatives.php` page on the site, there is a list of usernames to test with this
- `magna` is the only username that matches the `zA` rule with an a for the second and last letter
- From this we know
```
hE = m
zA = a
dC = g
fH = n
zA = a
```
- Reconstructing the password so far `magna__a______an_`
- After thinking on it, I realized that the resulting letter is the index of the sum of the indexes of the two letters
- ex// f = 6 and h = 8 becomes n = 14
```python
# yes you have to write it as a list don't use list(), I used code to do that cus I'm lazy
alphabet = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z']
cipher = ['hE', 'zA', 'dC', 'fH', 'zA', 'hA', 'iJ', 'zA', 'eI', 'aD', 'jB', 'cB', 'hH', 'gA', 'zA', 'fH', 'fN']

passwd = []

for letter in cipher:
    l1 = int(alphabet.index(letter[0].lower())) + 1
    l2 = int(alphabet.index(letter[1].lower())) + 1

    s = l1 + l2

    if s == 27:
        passwd.append('a')
    else:
        ch = alphabet[s - 1]
        passwd.append(ch)

print(''.join(passwd))
```
- Result: `magnaisanelephant`
```
magna@anonymous-playground:~$ ls
flag.txt  hacktheworld  note_from_spooky.txt
magna@anonymous-playground:~$ cat flag.txt
```
- First flag down two to go
