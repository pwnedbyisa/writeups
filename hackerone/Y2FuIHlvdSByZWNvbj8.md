flag 0
- the first thing I always check on web CTFs is the source code. fidning nothign of interest there, i moved on to web crawlers
- navigating to the `/robots.txt` path, i ended up with an aerror message that reveals specs about the apache server the website was running on
- `Apache/2.4.41 (Ubuntu) Server at 6b1332c976989145d1390daa07dd87bf.ctf.hacker101.com Port 80`
- after a bit of research, I found that apache 2.4 is vulnerable to `mod_rewrite`, which matches the hint (that says to rewrite the apache rule) for flag 0
- from the apache software foundation:
> By default, mod_rewrite maps a URL to a filesystem path. However, it can also be used to redirect one URL to another URL, or to invoke an internal proxy fetch.
- here's another source I used to better understand the setup: https://phoenixnap.com/kb/mod-rewrite
- in most cases, an `.htaccess` file is required to set up `mod_rewrite`, so I navigated to the filepath and got an access forbidden message
- another interesting thing was that most unknown filepaths threw a classic apache not found, but `index.php` threw a custom 'page not found' screen (meaning it probably exists)
- 
