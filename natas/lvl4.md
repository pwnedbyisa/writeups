> Access disallowed. You are visiting from "" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"
- request forgery time!
- I used curl to pass through the level credentials and modify the request location
```shell
curl http://natas4.natas.labs.overthewire.org/ -u natas4:tKOcJIbzM4lTs8hbCmzn5Zr4434fGZQm --referer http://natas5.natas.labs.overthewire.org/
```
