> this level provides an input box for a secret, a submit query button, and a link to view the source code (PHP, rather than just the websites view-source html)
- PHP snippet:
```php
<?

include "includes/secret.inc";

    if(array_key_exists("submit", $_POST)) {
        if($secret == $_POST['secret']) {
        print "Access granted. The password for natas7 is <censored>";
    } else {
        print "Wrong secret";
    }
    }
?>
```
- we include a file with a relative path of `includes/secret.inc`
- then we check to see if the user input actually contains data, and if it does, it is compared to the `$secret` variable
- if it's a match, we get the password

- so the first step is to figure out the value of `$secret`
- to do this I navigated to the `/includes/secret.inc` filepath
- viewing the source code, we get the string for the secret and input that for the password
