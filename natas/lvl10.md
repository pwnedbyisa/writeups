> literally just natas9 but you can't use these /[;|&]/
- they literally nuked all the sequential execution characters so we need a different angle
- the PHP code is basically identical except for the filter:
```php
if($key != "") {
    if(preg_match('/[;|&]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i $key dictionary.txt");
    }
}
```
- we can't execute commands, but we can try to modify what grep is search for
- right now, the query searches in dictionary.txt on a case insensitive basis
- if you've used grep before, you know that you can pass multiple files for grep to search
- so, if we were to input `a /etc/natas_webpass_natas10` as the `$key` value, we could force grep to also cat out info from the password file
- inputting a random character for grep to search for so it doesn't throw an error, the query would look like this:
```bash
grep -i a /etc/natas_webpass_natas10 dictionary.txt
```
- success!
