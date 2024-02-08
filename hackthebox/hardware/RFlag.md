WIP
> We have found the garage where some cyber criminals have all their stuff. Using an SDR device, we captured the signal from the remote key that opens the garage. Can you help us to analyze it?
#### Steps
1. Unzip the file w password `hackthebox` (given) and CD into the `hardware_rflag` directory
2. Basic info about the signal file
```
└─$ file signal.cf32             
signal.cf32: Adobe Photoshop Color swatch, version 0, 49212 colors; 1st RGB space (0), w 0xc0bc, x 0, y 0x803c, z 0; 2nd space (32956), w 0, x 0xc03c, y 0, z 0xc0bc
```
3. After looking up how to analyze `.cf32` files, I found the tool `rtl_433`
4. I installed it with `sudo apt-get install rtl-433` and ran `rtl_433 -A signal.cf32`
