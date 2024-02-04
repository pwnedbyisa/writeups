> I've hidden the flag very carefully, you'll never manage to find it! Please note that the goal is to find the flag, and not to obtain a shell. <br>
#### Steps
1. unzip the file with tha password `hackthebox` (given)
2. This reveals the `pwn_hunting` directory, which contains an ELF file cleed `hunting`
3. Running `file` on the ELF file, we get this:
```shell
hunting: ELF 32-bit LSB pie executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, BuildID[sha1]=801f10407444c1390cae5755d9e952f3feadf3eb, for GNU/Linux 3.2.0, stripped
```
4. Since it is stripped, it will be harder to read the binary
5. Running the binary does nothing; it just hangs for a bit and that's it. However, enetering a character while it hangs causes this:
```shell
└─$ ./hunting 
n
zsh: segmentation fault  ./hunting
```
7. Check binary protection with `checksec --file=hunting`
```shell
# result
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH      Symbols         FORTIFY Fortified       Fortifiable     FILE
Full RELRO      No canary found   NX disabled   PIE enabled     No RPATH   No RUNPATH   No Symbols        No    0               3               hunting
```
8. Not much to work with there so I ran the file with gdb like this: `gdb hunting`
9. Running gdb r and trying to list file mid-executing resulted in this
```shell
(gdb) r
Starting program: /home/isabelle/Downloads/pwn_hunting/hunting 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
ls

Program received signal SIGSEGV, Segmentation fault.
0xf7fc1000 in ?? ()
(gdb) 
```
10. Now we know the program crashes at address `0xf7fc1000` when given input
11. 
