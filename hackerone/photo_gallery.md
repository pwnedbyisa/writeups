WIP
flag 0
- the first thing that caugt my eye was the 'invisble' image at the bottom of the page. inspecting the source code, I found that all the images have an associated id
- messing with the id value didn't do much, but I wanted to take a closer look at the queries
- navigating to `/fetch?id=1` and 2 showed the binary content of the images, but `/fetch?id=3` threw a 500 internal server error
- now, anytime I hear the word 'query' the SQLi alarms start blaring
- since I'm still to lazy to effectively learn burpsuite, I decided to use SQLmap:
```SQL
sqlmap -u https://f1c22d5e504bcba8cc558c4cc18b17e0.ctf.hacker101.com/fetch?id=1 -dbs
```
- this returned a ton of vulnerabilities, including the fact that the website is running MySQL
- it detected the database `level 5`
- 
