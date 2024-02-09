> Easy leaks
#### Steps
1. Unzip the file w password `hackthebox` to get the `cat.ab` android backup file
2. I ran binwalk on the file, CDed into the extraction directory which contained `18` and `18.zlib`, and ran binwalk again
```
└─$ binwalk -e cat.ab

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Android Backup, compressed, unencrypted
24            0x18            Zlib compressed data, best compression

└─$ binwalk -e 18       

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             POSIX tar archive, owner user name: ".android.basicsmsreceiver/_manif", owner group name: "est"

```
3. The new extracted directory contains two folders `apps` and `shared`
4. I tried `grep -r HTB`, to recursively search the entire filesystem, but I had no luck there so I assumed the flag was either encoded or in an image
5. Because this challenge is supposed to be prettty easy I tried the image strategy first, navigating to `/shared/0/Pictures`
6. All of the pictures were of cats, except for `IMAG0004.jpg`, which showed a man with a clipboard labelled "Top Secret" where the flag was visible at the bottom
`HTB{ThisBackupXXXXXXXXXXXXX}`
