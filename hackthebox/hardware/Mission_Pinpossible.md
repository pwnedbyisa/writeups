WIP
> Our field agent cannot access the enemy base due to the password-protected internal gates, but observed that the password seemed to be partially displayed as it was typed into the security keypad. Thanks to an audacious mission, we were able to implant an embedded device into the wiring for the keypad's monitor, and intercepted some data. Your mission is to recover the password from the collected data.
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the files `op_pinpossible.logicdata` and `security_keypad.jpeg`
2. Gaining basic info about both of the files:
```
└─$ file op_pinpossible.logicdata 
op_pinpossible.logicdata: CLIPPER COFF executable C1 R1 not stripped - version 30 -Ctnc -Cdnc
```
![security keypad img](https://github.com/pwnedbyisa/writeups/assets/138353745/87e5d05a-1a79-40db-998d-9d53fe61cc6b)
3. I did further analysis of the `.logicdata` file with `strings` and `binwalk` (this is only a small section of the binwalk output, there were a lot more `Intel x86 lines`
```
└─$ strings op_pinpossible.logicdata      
Data save2
        Channel 0
        Channel 1
jTUU
rUK1
m1KU
        djhwV
jm`X
<^Hg

└─$ binwalk op_pinpossible.logicdata 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
116316        0x1C65C         mcrypt 2.2 encrypted data, algorithm: blowfish-448, mode: CBC, keymode: 8bit
195284        0x2FAD4         mcrypt 2.2 encrypted data, algorithm: blowfish-448, mode: CBC, keymode: 8bit
195324        0x2FAFC         mcrypt 2.2 encrypted data, algorithm: blowfish-448, mode: CBC, keymode: 8bit
240421        0x3AB25         Intel x86 or x64 microcode, pf_mask 0x665a, 1A2C-01-20, size 1
240453        0x3AB45         Intel x86 or x64 microcode, pf_mask 0x6664, 1BF7-01-20, size 1
240485        0x3AB65         Intel x86 or x64 microcode, pf_mask 0x666e, 1D87-01-20, size 1
240517        0x3AB85         Intel x86 or x64 microcode, pf_mask 0x6671, 1DF4-01-20, size 2
240581        0x3ABC5         Intel x86 or x64 microcode, pf_mask 0x667b, 2056-01-20, size 1
...
...
1891740       0x1CDD9C        mcrypt 2.2 encrypted data, algorithm: GOST, mode: CBC, keymode: 8bit
...
[ETC]
```

