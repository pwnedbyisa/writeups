- sort and uniq
- The password for the next level is stored in the file data.txt and is the only line of text that occurs only once
```bash
sort data.txt | uniq -u -c
```
- uniq only sorts out adjacent repeat lines, so sort is used to group all repeats together 
- this might be possible with grep, but I haven't figured that out yet 
