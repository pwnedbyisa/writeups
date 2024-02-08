> As a part of our SDLC process, we've got our firmware ready for security testing. Can you help us by performing a security assessment?
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the file `firmware.bin`
2. Running `file` and `binwalk` to find out more information
```
└─$ file firmware.bin 
firmware.bin: Linux kernel ARM boot executable zImage (big-endian)

└─$ binwalk firmware.bin 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Linux kernel ARM boot executable zImage (big-endian)
14419         0x3853          xz compressed data
14640         0x3930          xz compressed data
538952        0x83948         Squashfs filesystem, little endian, version 4.0, compression:xz, size: 2068458 bytes, 995 inodes, blocksize: 262144 bytes, created: 2021-03-11 03:18:10
```
3. From here I used `binwalk -e` to extract it, CDed into the extracted directory, and CDed into the `squashfs-root` directory
4. At this point I decided to connect to the instance with netcat `nc <IP> <PORT>`
5. It immediately prompted for a login for user `ng-1471916-hwtheneedle-j2sbl-64f9c49bff-jprgk`
6. I tried to grep that username recursively for the whole extracted directory, but no luck there
7. I then ran `grep -r login` which did return results (these are the most interesting)
```
squashfs-root/etc/scripts/telnetd.sh:           telnetd -l "/usr/sbin/login" -u Device_Admin:$sign      -i $lf &
squashfs-root/usr/lib/lua/luci/model/cbi/admin_system/admin.lua:ra = s:option(Flag, "RootPasswordAuth", translate("Allow root logins with password"),
squashfs-root/usr/lib/lua/luci/model/cbi/admin_system/admin.lua:        translate("Allow the <em>root</em> user to login with password"))
```
8. It appears that we now have a username, `Device_Admin`
9. From here we can search for the referenced `sign` file with `file / -name sign 2>dev/null` (the /dev/null ensures that all the permission denied errors aren't echoed to terminal)
```
/_firmware.bin.extracted/sign
/_firmware.bin.extracted/squashfs-root/etc/config/sign
```
10. The sign file contains `qS6-X/n]u>fVfAt!`, which I assume is the password
11. Now reconnect to the netcat instance, log in with the discovered creds, `ls` and cat out the flag.txt file
`HTB{4_hug3_blund3r_XXXXXX_!!}`
