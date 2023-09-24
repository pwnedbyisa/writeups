> another secret input webpage... yay...
- the source code in this one provides an encoded secret
```php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
```
- bin2hex in PHP is coverting the ascii chars to hexadecimal values
- this messed me up a bit because i'm used to decoding from the inside out, but for this you go outside in
- (tbh I should've caught that because the string is obviously hex values but anyways)
- first we convert hex to binary, then reverse the string, the base64 decode

