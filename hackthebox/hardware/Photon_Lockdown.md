> We've located the adversary's location and must now secure access to their Optical Network Terminal to disable their internet connection. Fortunately, we've obtained a copy of the device's firmware, which is suspected to contain hardcoded credentials. Can you extract the password from it?
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the `ONT` directory
2. The directory contains `fwu_ver`, `hw_ver`, and `rootfs`
```
└─$ file fwu_ver  
fwu_ver: ASCII text

└─$ file hw_ver 
hw_ver: X1 archive data
                                                                                                                                                                
└─$ file rootfs 
rootfs: Squashfs filesystem, little endian, version 4.0, zlib compressed, 10936182 bytes, 910 inodes, blocksize: 131072 bytes, created: Sun Oct  1 07:02:43 2023
```
3. Printing out the two small files I found `3.0.5` version number, and `X1` archive version
4. To analyze the `rootfs` filesystem, I made a temporary directory in my downloads folder and ran `sudo mount rootfs tmp` to mount it
5. CDed into the `home` directory, `ls -la`, find a hidden directory called `.41fr3d0`
6. In `.41fr3d0` is a file `s.txt`, which contains nothing but the text "almost there" </3
7. I then realized I was completely wasting my time because I already know the flag format, `HTB{}`
8. So I ran `grep -r HTB` and got the flag<br>
`HTB{N0w_Y0u_C4n_XXXXX}`
**Note**: you can use `sudo umount <dir>` to unmount the file system from the directory you placed it in and then delete the directory. Since the filesystem is read only, you can't delete it with rm
