WIP
> Suspicious traffic was detected from a recruiter&#039;s virtual PC. A memory dump of the offending VM was captured before it was removed from the network for imaging and analysis. Our recruiter mentioned he received an email from someone regarding their resume. A copy of the email was recovered and is provided for reference. Find and decode the source of the malware to find the flag.
#### Steps
1. First blood took 8 hours...I'm concerned
2. Anyways I started by unzippping the file with password `hackthebox`
3. That reveled a directory containing these files:
```shell
└─$ ls
flounder-pc-memdump.elf  imageinfo.txt  Resume.eml
```
4. `Resume.eml` contained some names and emails which could have been used for bruteforcing but this challenge has no instance so I left that for now
5. These are the contents of `imageinfo.txt`
```
└─$ cat imageinfo.txt 
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : VirtualBoxCoreDumpElf64 (Unnamed AS)
                     AS Layer3 : FileAddressSpace (/home/infosec/dumps/mem_dumps/01/flounder-pc-memdump.elf)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf800027fe0a0L
          Number of Processors : 2
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff800027ffd00L
                KPCR for CPU 1 : 0xfffff880009eb000L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2017-10-04 18:07:30 UTC+0000
     Image local date and time : 2017-10-04 11:07:30 -0700
```
6. Last I checked the gigantic elf file with `readelf -a`
7. The first part of the scan looked normal, but the description data at the end for `VBCPU` was unusual and contained a ton of zeroes
8. `radare2` also didn't work, because I couldn't seek any adresses (it always went back to `[0x00000000]>`)
9. 
