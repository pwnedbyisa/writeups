> ROT13 <br>
> The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions
```bash
# terminal method
cat data.txt | tr [A-Za-z] [N-ZA-Mn-za-m]
```
You can also copy the file into a ROT13 decoding website such as `dcode`
