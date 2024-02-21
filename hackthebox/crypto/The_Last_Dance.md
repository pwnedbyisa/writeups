> To be accepted into the upper class of the Berford Empire, you had to attend the annual Cha-Cha Ball at the High Court. Little did you know that among the many aristocrats invited, you would find a burned enemy spy. Your goal quickly became to capture him, which you succeeded in doing after putting something in his drink. Many hours passed in your agency's interrogation room, and you eventually learned important information about the enemy agency's secret communications. Can you use what you learned to decrypt the rest of the messages?
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the `crypto_the_last_dance` directory which contains a python script and output file
```python
# source.py
from Crypto.Cipher import ChaCha20
from secret import FLAG
import os


def encryptMessage(message, key, nonce):
    cipher = ChaCha20.new(key=key, nonce=iv)
    ciphertext = cipher.encrypt(message)
    return ciphertext


def writeData(data):
    with open("out.txt", "w") as f:
        f.write(data)


if __name__ == "__main__":
    message = b"Our counter agencies have intercepted your messages and a lot "
    message += b"of your agent's identities have been exposed. In a matter of "
    message += b"days all of them will be captured"

    key, iv = os.urandom(32), os.urandom(12)

    encrypted_message = encryptMessage(message, key, iv)
    encrypted_flag = encryptMessage(FLAG, key, iv)

    data = iv.hex() + "\n" + encrypted_message.hex() + "\n" + encrypted_flag.hex()
    writeData(data)
```
```shell
# out.txt
c4a66edfe80227b4fa24d431
7aa34395a258f5893e3db1822139b8c1f04cfab9d757b9b9cca57e1df33d093f07c7f06e06bb6293676f9060a838ea138b6bc9f20b08afeb73120506e2ce7b9b9dcd9e4a421584cfaba2481132dfbdf4216e98e3facec9ba199ca3a97641e9ca9782868d0222a1d7c0d3119b867edaf2e72e2a6f7d344df39a14edc39cb6f960944ddac2aaef324827c36cba67dcb76b22119b43881a3f1262752990
7d8273ceb459e4d4386df4e32e1aecc1aa7aaafda50cb982f6c62623cf6b29693d86b15457aa76ac7e2eef6cf814ae3a8d39c7
```
2. ChaCha20 requires a key and a *non reusable* initailization vector aka `iv` in the code above
3. Because `iv` is used twice and we know the relationship between the plaintext and ciphertext, we can break the encryption
4. (based on the python script, the top line is the `iv` hex code, the middle line is the message, and the bottom line is the flag)
5. I used `pwntools` for the xor operation, but if you don't want to install it there are tons of tutorials for doing it manually
```python
rom pwn import xor

# creating ciphertext variables
c_msg = '7aa34395a258f5893e3db1822139b8c1f04cfab9d757b9b9cca57e1df33d093f07c7f06e06bb6293676f9060a838ea138b6bc9f20b08afeb73120506e2ce7b9b9dcd9e4a421584cfaba248>
c_flag = '7d8273ceb459e4d4386df4e32e1aecc1aa7aaafda50cb982f6c62623cf6b29693d86b15457aa76ac7e2eef6cf814ae3a8d39c7'

# converting variables to bytes
c_msg = bytes.fromhex(c_msg)
c_flag = bytes.fromhex(c_flag)

# creating plaintext message variables
message = b"Our counter agencies have intercepted your messages and a lot "
message += b"of your agent's identities have been exposed. In a matter of "
message += b"days all of them will be captured"

pt = message

# plaintext xor message ciphertext xor flag ciphertext gives us the flag
flag = xor(c_msg, c_flag, pt)

print(flag)

```
```shell
└─$ python3 dance.py
b'HTB{und3r57AnD1n9_57R3aM_C1PH3R5_15_51mPl3XXXXXXXXX}\xaf\xe8\x15\x05\x7fK\xd6\xbdQ\xfb\x1a\xf7\xd3\xa7X0\x95J\x9f\x18\r\xc2u\xbe\xc0\x87\xd9\xf7_/)\x85\xb7\x08\x16\xf8\x9e\x93\x82n\xa1=\x8e\xe7W\xe6B\x9ceC\x95\xb3&\xd6y\x8b\xe2+\xc4\xc2\xfe3\xa0e\x01\xe5\x90\x95\xa9_\x8fh\xabDM|\x86\xd3\x02y\x89\xec\xe0/\xba\xfe\xf2V0f\x19\xa5)\xed\x1f\x06wEA\xc2_\xd0z\xce\x87'
```

