> There is nothing on this page
- this time, there were no leaked directories:
```html
<!-- No more information leaks!! Not even Google will find it this time... -->
```
- mentioning google triggered some alarm bells, and my mind immediately went to google web crawlers
- navigating to the commonly used `/robots.txt` I found this:
```
User-agent: *
Disallow: /s3cr3t/
```
- `/s3cr3t` ended up being another directory containing another users.txt file. success!
