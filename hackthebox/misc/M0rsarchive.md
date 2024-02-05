> Just unzip the archive ... several times ...<br>
#### Steps
1. From the description already I got binwalk locked and loaded lets get into it (spoiler: binwalk was not used </3)
2. I started by unzipping the file with the password `hackthebox` (given)
3. From that we get a `flag_999.zip` and `pwd.png` file
4. Steg is not my strongest point so I decided to start with the zip file
5. I ran `binwalk -e flag_999.zip` to extract, and cded into the new directory, which contained a `flag` directory
6. **Note** binwalk threw an error, and the png file was empty
7. Upon attempting to unzip the file manually, it prompted me for a password
8. At this point I checked the png file <br>
![image](https://github.com/pwnedbyisa/writeups/assets/138353745/b18812fb-6d1d-4e0a-a15e-691ae68a5e57)
13. It was very small, but it looks like morse code
14. Using an online decoder, I got the number `9`, which was the password to unzip the file
15. That creates a `flag/` directory
16. Now I need to write an script that reads the morse code, unzips the flag file with the password, and cds into the next flag directory
17. By 'I' I mean someone else (the person I stole it from stole it from someone else, just know it's not my code)
18. Simply put, the code reads pixel values to figure out the morse code, translates it to alphanumeric form, uses the new password for the zip file, and CDs into the new directory
```python
import re
import os
import sys
import zipfile
from PIL import Image


def get_pass(morse_list):
    password = ""
    MORSE_CODE_DICT = {'.-': 'a', '-...': 'b', '-.-.': 'c', '-..': 'd','.': 'e', '..-.': 'f', '--.': 'g', '....': 'h','..': 'i', '.---': 'j', '-.-': 'k', '.-..': 'l','--': 'm', '-.': 'n', '---': 'o', '.--.': 'p','--.-': 'q', '.-.': 'r', '...': 's', '-': 't','..-': 'u', '...-': 'v', '.--': 'w', '-..-': 'x','-.--': 'y', '--..': 'z', '-----': '0', '.----': '1','..---': '2', '...--': '3', '....-': '4', '.....': '5','-....': '6', '--...': '7', '---..': '8', '----.': '9','-..-.': '/', '.-.-.-': '.', '-.--.-': ')', '..--..': '?','-.--.': '(', '-....-': '-', '--..--': ','}
    for morse in morse_list:
        password += MORSE_CODE_DICT.get(morse)
    return password


def get_morse():
    fp = open('./pwd.png', 'rb')
    image = Image.open(fp)
    pixel = list(image.getdata())
    background = pixel[0]
    chars = []
    for i,v in enumerate(pixel):
        if v == background:
                chars.append(" ")
        else:
                chars.append("*")
    output =  "".join(chars)
    """正则匹配测试建议：https://regex101.com/
    ^  : asserts position at start of a line
    $  : asserts position at the end of a line
    \s : matches any whitespace character (equivalent to [\r\n\t\f\v ])
    *  : matches the previous token between zero and unlimited times, as many times as possible, giving back as needed (greedy)
    \* : matches the character *
    {3}: matches the previous token exactly 3 times
    """
    output = re.sub(r'^\s*', '', output)   #匹配开头的任意个空白字符，并替换为空
    output = re.sub(r'\s*$', '', output)   #匹配结尾的任意个空白字符，并替换为空
    output = re.sub(r'\*{3}', '-', output) #匹配3个*号，并替换为字符"-"
    output = re.sub(r'\*', '.', output)    #匹配单个*号，并替换为字符"."
    output = re.sub(r'\s{2,}', ' | ', output)  #(用于处理多行摩斯密码的情况)匹配两个以上空白字符，如果存在，就替换为"|"
    output = re.sub(r'\s', '', output)     #匹配空白字符，并替换为空
    output = output.split('|')
    fp.close()
    return output


def unzip_file(path, number, password):
    zip_path = "flag_" + str(1000-number) + ".zip"
    fp = zipfile.ZipFile(zip_path)
    for file in fp.namelist():
        fp.extract(file,"./",pwd=password.encode("utf-8"))
    fp.close()


def main():
    path = sys.path[0]            #当前脚本的运行目录

    for number in range(1,1001):
        print("Processing the "+ str(number) + "th archive.")
        #print(os.listdir('.'))   #显示当前目录下的所有文件
        morse_list = get_morse()
        password = get_pass(morse_list)
        unzip_file(path, number, password)
        path = "./flag"
        os.chdir(path)       #切换当前工作目录(进入flag子目录)

    fp = open('./flag', 'r')
    flag = fp.readlines()
    print(flag)
    fp.close()


if __name__ == "__main__":
    main()
```
19. I copied to code into the directory containing `flag_999.zip`, ran it, and got the flag!
```shell
Processing the 993th archive.
Processing the 994th archive.
Processing the 995th archive.
Processing the 996th archive.
Processing the 997th archive.
Processing the 998th archive.
Processing the 999th archive.
Processing the 1000th archive.
['HTB{D0_y0u_L1k3_XXXXX??}\n']
```
