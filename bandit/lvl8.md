> Sort and uniq <br>
> The password for the next level is stored in the file data.txt and is the only line of text that occurs only once
```bash
sort data.txt | uniq -u -c
```
- uniq only sorts out adjacent repeat lines, so sort is used to group all repeats together 
- this is probably possible with grep as well
