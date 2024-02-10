> Help! One of our red teamers has captured something between a user and their computer, but we've got no idea what we're looking at! Can you take a look?
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the `misc_mitm` directory and `mitm.log` file
``` shell
└─$ file mitm.log   
mitm.log: BTSnoop version 1,
```
2. The `BT Snoop` file format is used for storing Bluetooth Host Controller Interface (HCI) packets, typically on android devices
3. Knowing this, I opened the file with `wireshark`
4. The first 7 packets use the `HCI_MON` protocol and send initalization info like system note opcodes, version numbers, and device indexing
5. The rest of the packets follow the `L2CAP` protocol
6. Hovering over the hex values, wireshark shows the byte index. Within the `byte payload` (bytes 8-14) four bytes change from packet to packet
7. These are bytes 10 through 12
8. The majority of the frames have a length of 15, but a few have a length of 17
```
frame.len==15
frame.len==17
```
9. At this point I got kinda stuck, so I decided to investigate more about the device specs
10. Under the wireless tab, there is info on Bluetooth devices, HCI, etc
11. I started with Bluetooth devices. There was one listed with it's MAC address and OUI, `f8:28:19:e4:58:60     LiteonTe` 
12. Looking up LiteonTe, I got a ton of forums on people getting hacked and the unknown device showing up, or the unkown device just showing up on wifi networks
13. The Lite-On company makes many devices, one being bluetooth keyboards. Considering the circumstances, a keyboard makes the most sense
14. Based on the `A` present in the packet data, and some [documentation](https://cdn.sparkfun.com/datasheets/Wireless/Bluetooth/RN-HID-User-Guide-v1.0r.pdf) (page 11), we can determine how data is sent
![Screenshot 2024-02-10 110545](https://github.com/pwnedbyisa/writeups/assets/138353745/af8dda71-6e54-4b9d-aff7-57121cd03a27)
15. `00` precedes lowercase letters while `02` precedes uppercase letters (equal to hitting shift). `02 00 04` gives us `A`
16. To find the HTB string, we use the same strategy
```
frame[10] == 0x02 and frame[12] == 0x2F   --   {
frame[10] == 0x02 and frame[12] == 0x0B   --   H
frame[10] == 0x02 and frame[12] == 0x17   --   T
frame[10] == 0x02 and frame[12] == 0x05   --   B
frame[10] == 0x02 and frame[12] == 0x30   --   }
```
17. Extracting a flag of unknown length could take a very long time manually, so we write a script, passing through the key/value pairs for the bluetooth encoding.
18. I tried to stay true to my script kiddie roots and find an already written script to use, but unfortunately that wasn't possible so I had to write my own </3
19. Wireshark > save as > pcapng to make it easier to parse
20. jk I found someone elses script @johngrese on medium
```python
from scapy.all import *

pcap_file = 'mitm.pcapng'

KEYS = {
    0x1e: ('1', '!'),
    0x1f: ('2', '@'),
    0x20: ('3', '#'),
    0x21: ('4', '$'),
    0x22: ('5', '%'),
    0x23: ('6', '^'),
    0x24: ('7', '&'),
    0x25: ('8', '*'),
    0x26: ('9', '('),
    0x27: ('0', ')'),
    0x2c: (' ', ' '),
    0x2d: ('-', '_'),
    0x2e: ('=', '+'),
    0x2f: ('[', '{'),
    0x30: (']', '}'),
    0x31: ('\\', '|'),
    0x33: (';', ':'),
    0x34: ('\'', '"'),
    0x35: ('`', '~'),
    0x36: (',', '<'),
    0x37: ('.', '>'),
    0x38: ('/', '?'),
}

# populate a-z and A-Z
for offset in range(0, 26):
    KEYS[0x04 + offset] = (chr(0x61 + offset), chr(0x41 + offset))


def decode_bytes(data):
    decoded = ''
    for bytes in data:
        shift = bytes[0] == 0x02
        code = bytes[2]
        if code in KEYS:
            decoded += KEYS.get(code)[1 if shift else 0]
    return decoded


def parse_capture():
    capture = rdpcap(pcap_file)
    data = []
    for packet in capture:
        raw_data = packet.getlayer(Raw).load
        frame = raw_data[4:]
        if len(frame) == 17:
            data.append(frame[10:13])
    return data


if __name__ == '__main__':    
    data = parse_capture()
    flag = decode_bytes(data)
    print(flag)
```
21. Ran it and got the flag `HTB{K3yStR0keS_C0mPrXXXXXX}`
