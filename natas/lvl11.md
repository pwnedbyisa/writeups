> Cookies are protected with XOR encryption
> Background color: (input box to set the color with hexcode)
> + a link to view source code
- in the source code there is a `showpassword` variable that is set to no based on the data in the cookie, if we can modify that data, we ca display natas12 pass
- using inspect, i found that the data cookie has a value of `MGw7JCQ5OC04PT8jOSpqdmkgJ25nbCorKCEkIzlscm5of3l9en95bjY%3D`
- to find out how to decode it, we look at this part of the php code:
```php
function loadData($def) {
    global $_COOKIE;
    $mydata = $def;
    if(array_key_exists("data", $_COOKIE)) {
    $tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);
    if(is_array($tempdata) && array_key_exists("showpassword", $tempdata) && array_key_exists("bgcolor", $tempdata)) {
        if (preg_match('/^#(?:[a-f\d]{6})$/i', $tempdata['bgcolor'])) {
        $mydata['showpassword'] = $tempdata['showpassword'];
        $mydata['bgcolor'] = $tempdata['bgcolor'];
        }
    }
    }
    return $mydata;
}

function saveData($d) {
    setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
}
```
- we know that the data is encoded with b64, xor, and json
- to actually figure out the decoding process, we need to understand how the xor function works
```
function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;

}
```
- xor uses a predefined key and the plaintext input to create the encrypted data
- pt (xor) key = enc
- enc (xor) key = pt
- pt (xor) enc = key
- in this case, the encrypted text is the cookie (which we have) and the plaintext is the array (which we input)
- first, we create a script base64 decode the cookie to get it to it's xor encrypted format and input that as plaintext
- next, we set the key as the json encoded default array
```php
<?php

$cookie=base64_decode('MGw7JCQ5OC04PT8jOSpqdmkgJ25nbCorKCEkIzlscm5of3l9en95bjY%3D');

function xor_encrypt($in) {
    $key = json_encode(array( "showpassword"=>"no", "bgcolor"=>"#ffffff"));
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

echo xor_encrypt($cookie);
?>
```
- ran the script with `php test.php` and get the key `KNHLKNHLKNHLKNHLKNHLKNHLKNHLKNHLKKL`
- if a key is shorter than the plaintext it just repeats, so we know the key is `KNHL`
- now that we have the key, we can modify the code to combine it with the array when the showpassword value is set to 'yes'
```php
<?php

$data= array( "showpassword"=>"yes", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = 'KNHL';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

echo base64_encode(xor_encrypt(json_encode($data)));
?>
```
- that returns this new value `MGw7JCQ5OC04PT8jOSpqdmk3LT9pYmouLC0nICQ8anZpbS4qLSguKmkz`
- setting this as the new cookie value, we get the password `YWqo0pjpcXzSIl5NMAVxg12QxeC1w9QG`
