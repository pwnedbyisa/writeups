flag 0
- tbh I had no idea where to start, so I did my first checklist item for most web CTFs; add robots.txt to the end of the url to check for web crawlers (for reference, .htaccess and another one imma add when i remember what it's called are also good starting points)
- the page showed `User-agent: *` and  `disallow /s3cr3t-ar3a`, along with the flag

flag 1
- my next step was to navigate to the forbidden filepath, which had a message saying the page was moved and peope who are allowed access know where to go
- you know what that means... directory busting time! (spoiler alert this was a waste of 8 minutes)
- unfortunately dirb turned up nothing so I decided to dig a bit more on the webpage
- I used inspect to dig through the HTML and quite easily found another flag in the `alertbox` id

flag 4
- in the same alertbox that contained flag 1, there was a `next-page` attribute that pointed to the filepath `/apps-home`
- this page contains a few differen buttons, including one for a secure login portal
- clicking on the secure login page created a popup that prompted you to start a challege to access the secret area
- SQL injection didn't work, but the error message was `invalid unsername`, which is a pretty unique reponse
- from here, I tried 'grinch' and 'admin', effectively gave up, and decided to bruteforce for a valid username using wfuzz
```bash
wfuzz -zfile,/usr/share/wfuzz/wordlist/general/common.txt --hs 'Invalid Username' -d 'username=FUZZ&password=grinch' https://66eb8eed35791300628d3d4f7b00bccf.ctf.hacker101.com/secure-login/
```
- that returned the username 'access', and when tested on the portal, I now get the error `invalid password`
- some more bruteforcing 
```bash
ffuf -u https://hackyholidays.h1ctf.com/secure-login -X POST -t 50 -fr "Invalid Password" -d "username=access&password=FUZZ" -H "Content-Type: application/x-www-form-urlencoded" -c -w /usr/share/wordlists/seclists/Passwords/Common-Credentials/10-million-password-list-top-100.txt
```
- which returned 'computer'
- I logged in, and got brought to a screen that said 'no files to download'
- immediately going to inspect, I found that there was a new cookie with the name `securelogin`
- inspecting the cookie values, I noticed that the `secure` token was set to `false`. I couldn't figure out what controlled this token, so I decided to come back to it later.
- I then noticed that the value of the `securelogin` token, `eyJjb29raWUiOiIxYjVlNWYyYzlkNThhMzBhZjRlMTZhNzFhNDVkMDE3MiIsImFkbWluIjpmYWxzZX0%3D`, looked like it was base64 encoded
- that returned this: `{"cookie":"1b5e5f2c9d58a30af4e16a71a45d0172","admin":true}7`
- so I set the admin value to true, re-encoded it and modified the token value. (i also removed the 7 before encoding, idk why it's there)
- that brought up a `my_secure_files_not_for_you.zip` file, which contains a password protected png file, and a password protected flag file
- after a bit of research, I discovered fcrackzip and bruteforced the flag.txt password
`fcrackzip -u -D -p wordlists/passwords.txt my_secure_files_not_for_you.zip`
- the password was 'hahahaha'

flag 2
- ik these are out of order, but secure login looked most interesting anyways
- next I went to people rater, which was a page with a list of names as buttons and the option to load more
- clicking on a name pushed an alert to your screen with some random negative adjective, but no changes to the url were made
- after checking inspect, I noticed each person has a similar id that looks like base64 encoded data
- decoding Tea Avery's returned `{"id":2}`
- since that name is the first on the list, I decided to try and change the id to 1 by base64 encoding the same format
- this made the response concerningly positive, but no flag. it seemed like there was imformation I wasn't seeing, so I kept digging
- going to the network tag in inspect, I noticed that when a button is pressed, a request to a JSON page is made
- I decided to try and navigate to the JSON version of the page, by using the id=1 filepath
`https://66eb8eed35791300628d3d4f7b00bccf.ctf.hacker101.com/people-rater/entry/?id=eyJpZCI6MX0K`
- success! (the id belonged to the grinch, of course)

flag 3
- I was 100% stuck with this one so I decided to look up a writeup for it (spoiler alert they started talking about APIs, I got scared, we'll come back to it maybe)

flag 5
- navigating to the diary button, the first thing I notcied was the url path `/my-diary/?template=entries.html`
- trying to go the the `/my-diary` path just redirects you to the template path
- next I just tried vieweing the `/my-diary/entries.html` filepath itself, which worked
- this means that the `template` directory was loading files that it itself contained
- knowing this, we can try to access other files within the `template` directory
- I tried to access an index file using `index.html` (didn't work) and `index.php` (worked!)
```php
<?php
include_once('../../Flags.php');
if( isset($_GET["template"])  ){
    $page = $_GET["template"];
    //remove non allowed characters
    $page = preg_replace('/([^a-zA-Z0-9.])/','',$page);
    //protect admin.php from being read
    $page = str_replace("admin.php","",$page);
    //I've changed the admin file to secretadmin.php for more security!
    $page = str_replace("secretadmin.php","",$page);
    //check file exists
    if( file_exists($page) ){
        $contents = file_get_contents($page);
        $contents = str_replace('!'.'FLAG!',Flags::get(5),$contents);
        echo $contents;
    }else{
        //redirect to home
        header("Location: ../my-diary/?template=entries.html");
        exit();
    }
}else{
    //redirect to home
    header("Location: ../my-diary/?template=entries.html");
    exit();
}
```
- I navigated to /my-diary/secretadmin.php, and got the message `You cannot view this page from your IP Address`
- the reason why this doesn't work is because of the character replacement, so the filepath needs to be modified, so if admin.php and secretadmin.php are removed, it still reads secretadmin.php
- `secretadsecretaadmin.phpdmin.phpmin.php`
- remove admin.php
- `secretadsecretadmin.phpmin.php`
- remove secretadmin.php
- `secretadmin.php`
- replace the filepath - `/my-diary/?template=secretadsecretaadmin.phpdmin.phpmin.php`
- success! (you also get a pending entry about how the grinch is gonna DdoS santa's workshop)

flag 6
- the next challenge is the grinch's hate mail campaign
- I navigated to the only avaiable campaign name (called Guess What)
- the hate mail which grabs a couple templates and the name of the recipient, and you can view the result witht he preiveiw button
- my first though was to see if we can get it to output file contents that it's not supposed to, because it's pulling those templates
- I previewed a new campaign passing flag.txt to the template as a test, and I got the message `Cannot find template file /templates/flag.txt`
- navigating to `/hate-mail-genorator/templates` all the files in the directory were listed out
- one of the files was `38dhs_admins_only_header.html`
- trying to navigate to the filepath threw a 403 forbidden, and trying to preview an email with the file resulted in a `You do not have access to the file 38dhs_admins_only_header.html` message
- inspecting the campaign form further, I found some hidden elements, including the recipients name and email
- I modified the hidden preiview data block to include the admin only template (adding the template value normally threw a JSON formatting error, so I replaced the name with the template)
- got the super secret admin header + the flag

flag 7
- next we tackle the forum
- the first thing I noticed was that some posts are hidden unless you are an admin
- vewing regular posts, there is an index in the url, but trying to view /0, /3, /4, /1/0, /1/2, etc didn't work
- next I noticed the login option; this time, is said 'user/pass combo is invalid', which I intepreted as 'SQL time!'
- this ended up being a dead end, so I fuzzed for directories instead
```bash
ffuf -u https://hackyholidays.h1ctf.com/forum/FUZZ -fc 404 -c -t 50 -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```
- I found the path `/phpmyadmin`
- now we get the REAL login screen
- looking up phpmyadmin on google, github results immediately began to pop up
- most of the github repos mentioned using mysql, so I tried to login using SQL bypass techniques
- that didn't work out, so I kept digging (default creds also didn't work)
- as a kind of last ditch attempt, I just looked up 'phpmyadmin grinch' on google, which brough up a repo called forum run by Grinch-Networks
- it says there were only 4 commits to the repo (which I found immediately suspicious but that could js be me)
- I went striaght to the changelog, (where I immediately realized why my SQL attempts weren't working)
- manually checking the commits, I found one called small fix
- this line of code:
```php
self::$read = new DbConnect( false, 'forum', 'forum','6HgeAZ0qC9T6CQIqJpD' );
```
- was changed to this
```php
self::$read = new DbConnect( false, '', '','' );
```
- shout out to leaked credentials fr 
- I logged in with the combo, clicked on forum, users, and found two users with matching hashes
- grinch had a 1 in the admin column, so I cracked his hash through basic lookup
- the password ended up being `BahHumbug`
- logging in to the regular portal, there ended up being a new section of posts called 'Secret Plans', which contained a post with the flag

flag 8
- quiz time! I entered grinch as my name, left the default answers and submitted
- at the end of the quiz, I got a message saying there was one other player with the same name, which means the quiz stores player names, which means theres a database, which FINALLY means SQLi
- fun fact idk enough about SQL to exflitrate w this one... so we'll come back later

flag 9
- I was so traumatized by all the login portals atp that I just decided to inspect the source code and page elments first
- the top of the webpage source code had this message `<!-- See README.md for assistance -->` so I navigated to the `/README.md` filepath, which downloaded the file
```
# SignUp Manager

SignUp manager is a simple and easy to use script which allows new users to signup and login to a private page. All users are stored in a file so need for a complicated database setup.

### How to Install

1) Create a directory that you wish SignUp Manager to be installed into

2) Move signupmanager.zip into the new directory and unzip it.

3) For security move users.txt into a directory that cannot be read from website visitors

4) Update index.php with the location of your users.txt file

5) Edit the user and admin php files to display your hidden content

6) You can make anyone an admin by changing the last character in the users.txt file to a Y

7) Default login is admin / password
```
- first I tested the default creds (didn't work)
- then I tried navigating to `/users.txt`, `/signupmanager.zip`, and `/index.php`
- first threw a 404, zip file downloaded, and index was just the homepage
- the zip file contained `user.php`, `admin.php`, `signup.php`, `index.php`, and a README file
- user.php contains this (admin.php is basically identical):
```php
<?php
//DO NOT DELETE THE BELOW LINE
if( !isset($page) ) die("You cannot access this page directly"); ?>
<!DOCTYPE html>
<html lang="en">
<head>
    <title>SignUp Manager - User Area</title>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
</head>
<body>
<div class="container" style="margin-top:20px">
    <h1 class="text-center" style="margin:0;padding:0">User Area</h1>
</div>
</body>
</html>
```
- so naviagating to the filepath works about as well as expected
- based on step 6 in the README, you can also elevate any user to admin privileges by modifying their users.txt file to have a Y as the last character
- in the `index.php` file (which I will not be pasting cus it's long), I noticed that the only parameter limited by length is age, which means theoretically we can add enough Y's (or somehow make othe rparameters long enough) to overwrite into admin permissions
- the final field in the user.txt file is the user's lastname, which means the last letter must be Y.
- after a couple tests, I quickly realized that it would be a lot more efficient to make the age as big as possible with as few characters as possible, rather than mak out on characters with the names
- I used E notation to fit the 3 character requirement (ex// 2e3 is 2 * 10^3), changed the form with inspect, and made my lastname all Y's
- success!

flag 10 + 11
- wip
- these both take place on the `/r3c0n_server_4fdk59/`
