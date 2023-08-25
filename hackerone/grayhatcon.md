WIP
env: hackerbay, a website where a bunch of stuff is being sold, someone posted a user/pass breach, hack in and take it down.

- flag 0
	- remembered a website crawling tip from past CTFs, checked the robots.txt filepath
	```
	User-agent: *
	Disallow: s3cr3t-4dm1n/
	```
	- when I navigated to the filepath, I got  a 403 forbidden, not a 404 not found
	- the * means it applies to all robots, and the specified path is where the robots are not allowed to go
	- might be a directory, so I scanned the path with dirb - found: /s3r3t-4dm1n/.htaccess
	- navigating to that filepath, the .htaccess file is downloaded, which contains the contents:
	```
	Order Deny,Allow
	Deny from all
	Allow from 8.8.8.8
	Allow from 8.8.4.4
	```
	- so I used curl to change my access IP (testing both allowed IPs):
	```bash
	curl -H 'X-Forwarded-For: 8.8.8.8' <url/s3cr3t-4dm1n>
	curl <url/s3cr3t-4dm1n> -H 'X-Forwarded-For: 8.8.8.8'
	```
	- I still got a 403 error (which was very confusing)
	- tried running it in powershell with curl.exe, same response
	- basically threw in the towel atp and found another writeup; long story short they followed the same steps and got the flag so I'm convinced curl is broken or there's an interference with my VM
  - (you should get the flag tho)

- flag 1
	- usernames are shown next to listings (which took me an embarrassingly long time to figure out)
	- so I reset the password as hunter2
	- after using inspect (and digging through like 17000 layers of divs) I found the account hash is stored in a hidden value, and hunter2's is value="cf505baebbaf25a0a4c63eb93331eb36"
	- after that I tried to access hunter2's auction page:
	``` bash
	curl --cookie 'userhash=cf505baebbaf25a0a4c63eb93331eb36' https://9ab51d50439ae311e06f11eacf66893d.ctf.hacker101.com/dashboard/auctions
	```
	- which didn't work
	- my next thought was to enable a subuser (which handles the main user's auctions) using hunter2's hash
	- inspecting the subuser under form method post we get:
	```html
    <input type="hidden" name="owner_hash" value="my acc hash value">
	```
	- there are two other parameters needed to create a subuser, new_username and new_password
	- I created a new subuser account using curl (x form post method, if you're using httpie I think it's -v POST or smth)
	```bash
	curl https://9ab51d50439ae311e06f11eacf66893d.ctf.hacker101.com/register -d 'owner_hash=cf505baebbaf25a0a4c63eb93331eb36&new_username=222&new_password=password'
	```
	- then I logged in with the new account, and I got a  'account waiting for activation' message along with a flag

- flag 3
	- to enable user 222, I log in with my other account (111), and use the same form post method, but instead change the subuser hash to 222's hash, and the userhash cookie to hunter2's hash. 
	- change the enable_toggle=enable (tested w disable too)
	```bash
	curl https://9ab51d50439ae311e06f11eacf66893d.ctf.hacker101.com/dashboard/subusers --cookie 'userhash=cf505baebbaf25a0a4c63eb93331eb36' -d 'hash=f2022076ad3ce0245c5e416b272be4d0&enable_toggle=enable'
	```
	- this isn't working, tried it w verbose mode for troubleshooting (slept through my self taught networking course on youtube university so that was also a bunch of gibberish)
	- I ended up changing the cookie to the actual session token (logged in as user 111), keeping the hash as subuser 222's, and changing the enable toggle
	```bash
	curl -v https://9ab51d50439ae311e06f11eacf66893d.ctf.hacker101.com/dashboard/subusers --cookie 'token=YjQ4OTZjZjQyNDQwYTdhYTIxNTYwYzAxZjUyN2ZmYTVjN2JhYjRkNGY1YzY2NjcyYzQ2NjdmYTUxZjZkMGQxNTU4NDM3ZGZkYzU2ODkxYjM0OWVkM2M2ZWE1ODc2NDI0M2MzOWJjMDFkMDkxZWQ1NzdmYTcxNWY1OGJlODAwM2U%3D' -d 'hash=f2022076ad3ce0245c5e416b272be4d0&enable_toggle=enable'

	```
	- the flag here was under user details on login

- flag
	- next, I went to my auctions on subuser 222
	- viewing the source code, there is a section called auction_type_id in the form-control class, which shows options for id's numbered 0-5
	- there is also a class called auction_questions. when I tried to navigate to the /auctions/questions filepath, I got an invalid id error `{"error":"Missing id parameter"}
	- so I navigated to the same filepath adding the id parameter /dashboard/auctions/questions?id=1, which displays this as raw data: 
	```json
	{"name":"Desktops","questions":[{"question":"Make","field_name":"field_4728_186574"},{"question":"Model","field_name":"field_5738_281961"},{"question":"RAM","field_name":"field_5051_369408"},{"question":"Processors","field_name":"field_4032_347621"}],"auctions":[{"id":"1","title":"Ultra Desktop"},{"id":"9","title":"Premium Desktop"},{"id":"10","title":"Work Station"}]}
	```
	- changing the id to values like 0 or 12 returns an invalid auction id error
	- SQL is the way to go in this case - end goal obtain the admin user and password
