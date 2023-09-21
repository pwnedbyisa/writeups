WIP
flag 0
- the first thing I always check on web CTFs is the source code. finding nothing of interest there, i moved on to web crawlers
- navigating to the `/robots.txt` path, i ended up with an aerror message that reveals specs about the apache server the website was running on
- `Apache/2.4.41 (Ubuntu) Server at 6b1332c976989145d1390daa07dd87bf.ctf.hacker101.com Port 80`
- after a bit of research, I found that apache 2.4 is vulnerable to `mod_rewrite`, which matches the hint (that says to rewrite the apache rule) for flag 0
- from the apache software foundation:
> By default, mod_rewrite maps a URL to a filesystem path. However, it can also be used to redirect one URL to another URL, or to invoke an internal proxy fetch.
- here's another source I used to better understand the setup: https://phoenixnap.com/kb/mod-rewrite
- in most cases, an `.htaccess` file is required to set up `mod_rewrite`, so I navigated to the filepath and got an access forbidden message
- another interesting thing was that most unknown filepaths threw a classic apache not found, but `index.php` threw a custom 'page not found' screen (meaning it probably exists)
- I tried uploading a blank php file to the website to see if there's the possibility of code execution
- surprise surprise, you aren't allowed to upload php files (boooooooo)
- after a bit of research, I discovered, `.phtml` files, which contain both php and html content (and aren't blocked by the website's filters)
- now we can make a custom request through burpsuite to execute php code (and do something like list out files)

flag 1
