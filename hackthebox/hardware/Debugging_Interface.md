> We accessed the embedded device's asynchronous serial debugging interface while it was operational and captured some messages that were being transmitted over it. Can you decode them?
#### Steps
1. Unzip the file, password `hackthebox` (given)
2. This reveals a `.sal` file
3. I started by using running `strings` on the file, which uncovered a few interesting lines, one of them being `meta.json`
4. To try and extract these files, I ran `binwalk -e debugging_interface_signal.sal`
```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Zip archive data, at least v2.0 to extract, name: digital-0.bin
5390          0x150E          Zip archive data, at least v2.0 to extract, name: meta.json
9675          0x25CB          End of Zip archive, footer length: 22
```
5. Viewing the `meta.json` file, I noticed some interesting information at the end of the file
```json
{"type":"digitalTrigger"}}},"binData":[{"type":"Digital","index":0,"file":"./digital-0.bin"}]}
```
6. It appears as if the other file, `digital-0.bin` is being used to run something
7. Running `file` on `digital-0.bin` just returns data, so I decided to cat it out for a closer look
8. Some of the beginning of the file:
```
<SALEAE>d�AK��^xffffff�?:�����@���3����@��3�L����@���Lf����@��f�����@��������@���������@
���������@���������@���������@����▒����@���▒2����@��2�K����@���Ke����@��e�~����@���
~�����@���������@���������@���������@���������@��������@���1����@��1�J����@���Jd��
��@��d�}����@���}�����@���������Lc�~L@YL@YL@LALALAL@LALAYYLAL@LAL@YYLALALAL@LALAeDeDLAL@LALALAL@eEL@LALAL
A@�       LAYLAL@LA~HLAL@LALAYeDYL@LAeDLALAeDL@LBL@LAYLALAYLALAYYL@eEL@LAL@LAYLAL@YL@LAeDLAL@eEL@LALALAYrLALA@�   LAYLAYY
YYL@LAYLAYeEL@LALArL@YL@LBL@LAL@YL@YLALAYLAL@eEYLA@�     LAYLALALAL@LAYYL@LALAYeCYLALAL@LAeDYYLAL@YeDYL@LAL@eEL@YYLALALAL
@LAYYL@LBL@eDLAYYLAeDLAL@YYLAeDLAL@YYLALALAYeDYLALAYYYYLAeCLBL@YYLAL@LAeDYYLAYLAeDYL@LALALAL@LALAYYLBreEYLAeDLAL@YYLAL@eEL@
YYLAreDYLALAYeCYLALAL@LAYeEYLAL@YYYYLALAYeDYL@LAYLBYYYLAL@eEL@YYLAL@LALALAYYL@LAL@LArYL@LAYYLAYYLBL@LAL@LAYYLALA~GYYLAreDYLA
LALArYL@LALALAL@LALAYYLAL@YYYYLAL@YeDYL@LALAYeDYL@LAreDYLAL@YYYYLAeDLAL@YYLAYLAYYYLALAeDLAYYLBL@LAYeEYLAL@LALALAYYL@LAL@LAeD
YYLAYYL@YYLAeDLALAYYLAL@YYYF2�0
```
9. I focused on the `<SALEAE>` a the beginning of the file
10. That brought me to this website: https://www.saleae.com/ which deals with hardware debugging so it checks out
11. They also advertise a tool to analyze captured adaa from devices (which I downloaded)
12. I used `chmod +x` to make it executable, and run it
13. I opened a new capture with the `debugging_interface_signal.sal` file, and double clicked the capture box until i could see the individual waves
14. Hovering over the wave blocks show the bit rate, with is 32.0 beginning at the start block
15. Go to analyzers and click async serial, then chose `32000` for the bit rate (an estimate) and channel 00 if prompted
16. Scroll down to data, click terminal, and the flag is at the bottom <br>
`HTB{d38u991n9_1n732f4c35_c4n_83_f0und_1n_41m057_3v32y_3m83dd3d_XXXXXXXXXX}`
