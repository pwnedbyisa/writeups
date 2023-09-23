> This level contains (very bare-bones) home and about pages
- viewing the source code reveals this:
```html
<a href="index.php?page=home">Home</a>
<a href="index.php?page=about">About</a>

<!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->
```
- the index pages are set up a way that could allow something other than an html file to be passed through
- so i modified the url like this `index.php?page=/etc/natas_webpass/natas8`
- success!
