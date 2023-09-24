> setup is a search bar to 'find words containing' _____ + source code
- php code:
```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    passthru("grep -i $key dictionary.txt");
}
?>
```
- one thing that immeditaely stood out to me was the reference to the `dictionary.txt` file
- navigating to the path, I found a pretty long (kinda odd) dictionary
- yeah you could just look through the whole thing, but that would take a million years (and i'm like 87% sure the password isn't in there)
___
- the second thing I noticed was the url when the passthru function was called
- `http://natas9.natas.labs.overthewire.org/?needle=yourstringhere&submit=Search`
- passthru is used to execute a function as it would be on the command line 
- it's specifially vulnerable to command injection which allows for sequential execution (aka running a bunch of commands on one line separated by semicolons)
- the query `str; ls -la` returns this:
```
-rw-r----- 1 natas9 natas9 460878 Apr 23 18:01 dictionary.txt
```
- so I added a bunch of ../ to file traverse and navigate to the /etc dir that holds the passwords
- after a bit of messing around I got a working query:
- `;cat ../../../../etc/natas_webpass/natas10`
