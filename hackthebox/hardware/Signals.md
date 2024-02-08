> Some amateur radio hackers captured a strange signal from space. A first analysis indicates similarities with signals transmitted by the ISS. Can you decode the signal and get the information?
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the `Signal.wav` file
2. First I went to [dcode](https://www.dcode.fr/spectral-analysis) to run the file through spectral analysis, but I didn't find much there
3. Looking at the challenge description again, the ISS is mentioned
4. After exactly one google search ("iss signal transmission file type"), I found that the ISS uses the `PD-120 SSTV` format (sstv is the important bit)
5. Now I looked for tools used to decode SSTV images, and I ended up with `mmsstv`
6. The [webpage](https://hamsoft.ca/pages/mmsstv.php) was a lil sketch but I haven't seen signs of malware since downloading it so it was probably fine
8. To send the sound from the file directly to `mmsstv`, we need a [digital cable](https://vb-audio.com/Cable/) and [vlc](https://www.videolan.org/vlc/) player
9. I used [this article](https://oe5lxr.at/decode-sstv-with-mmsstv/) for help with setting everything up
10. After the file is done playing, you should have a red background, skull and crossbones, and readable flag on your screen
11. If you're having issues, you can also try `rxsstv` <br>
`HTB{5l0w-5c4n_73l3v1510n_h4m_r4d10_XXXXXX}`
