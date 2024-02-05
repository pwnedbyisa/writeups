> You are after an organised crime group which is responsible for the illegal weapon market in your country. As a secret agent, you have infiltrated the group enough to be included in meetings with clients. During the last negotiation, you found one of the confidential messages for the customer. It contains crucial information about the delivery. Do you think you can decrypt it?
#### Steps
1. Unzip the file, password `hackthebox` (given)
2. We have two files, a python script and `msg.enc` which contains this string:
```
6e0a9372ec49a3f6930ed8723f9df6f6720ed8d89dc4937222ec7214d89d1e0e352ce0aa6ec82bf622227bb70e7fb7352249b7d893c493d8539dec8fb7935d490e7f9d22ec89b7a322ec8fd80e7f8921
```
3. This is the python script, `chall.py`:
``` python
import string
from secret import MSG

def encryption(msg):
    ct = []
    for char in msg:
        ct.append((123 * char + 18) % 256)
    return bytes(ct)

ct = encryption(MSG)
f = open('./msg.enc','w')
f.write(ct.hex())
f.close()
```
4. Reading/ running the script, we find that the secret module does not exist, and the string library isn't even used
5. Writing a decryption script (literally just reversing everything in the above script) - you can also UTF-8 encode it, but the bytes were readble so I didn't
```python
def decryption(msg):
  ct = []
  for char in msg:
    char = char - 18
    char = 179 * char % 256
    ct.append(char)
  return bytes(ct)

with open('msg.enc') as f:
  ct = bytes.fromhex(f.read())

ct = decryption(ct)
print(ct)
```
6. Success!
`
b'Th3 nucl34r w1ll 4rr1v3 0n fr1d4y.\nHTB{l00k_47_y0u_r3v3rs1ng_3qu4710n5_XXXXXXXX}'
`
