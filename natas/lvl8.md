> another secret input webpage... yay...
- the source code in this one provides an encoded secret
```php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
```
- all you have to do is b64 decode, reverse the string, and convert the hexocde to binary

- passwd for 8 so i can pick up where i left off: a6bZCNYwdKqN5cGP11ZdtPg0iImQQhAB
