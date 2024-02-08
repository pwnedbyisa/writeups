WIP
> Our infrastructure is under attack! The HMI interface went offline and we lost control of some critical PLCs in our ICS system. Moments after the attack started we managed to identify the target but did not have time to respond. The water storage facility's high/low sensors are corrupted thus setting the PLC into a halt state. We need to regain control and empty the water tank before it overflows. Our field operative has set a remote connection directly with the serial network of the system.
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the `PLC_Ladder_Logic.pdf` and `interface_setup.png` files
2. Ladder logic is used to create software to run PLCs, where each device (such as a switch or relay) is represented on a series of horizontal rails to show logical progression
3. For example, -| |- is used to represent an open connection, and -|/|- represents a closed connection 
4. Here is the interface setup image
![interface_setup](https://github.com/pwnedbyisa/writeups/assets/138353745/1a83dc50-28bc-4907-a7cd-4437ecb8b0a5)
5. Next I decided to check out the running isntance
6. Upon connection with `nc IP PORT` and selecting 1, I got this output:
```
└─$ nc 94.237.56.248 48544  
Water Storage Facility Interface
1. Get status of system
2. Send modbus command
3. Exit
Select: 1
{"auto_mode": 1, "manual_mode": 0, "stop_out": 0, "stop_in": 0, "low_sensor": 0, "high_sesnor": 0, "in_valve": 1, "out_valve": 0, "flag": "HTB{}"}
```
7. Remembner that our task is to open the `in_valve` and close the `out_valve`. At this point I went back to review the ladder logic
![image](https://github.com/pwnedbyisa/writeups/assets/138353745/bb16fdda-994e-4910-8a00-e25aeeeb0c6e)
9. For this first segment, when start is enabled it travels through `manual_mode_control` to `auto_mode`, which jummps down a rung to enable `manual_mode`
![image](https://github.com/pwnedbyisa/writeups/assets/138353745/4a6913cc-44b7-4fdd-817e-6462c68cd3ac)
10. Next we run `force_start_out` to follow the path needed to run `out_valve`, remember that we closed `manual_mode` earlier
11. (force_start_out is enabled by running the second to last rung of the below section)
![image](https://github.com/pwnedbyisa/writeups/assets/138353745/932421ce-f0b8-46b3-8339-75d78e0501f2)
12. We run the bottom rung of this section to close `force_start_in` and enable the `in_valve`
13. Next we create the payload to submit the modbus command
14. Structure: Hex PLC address (2 chars), Hex function code (2 chars), Hex register address (4 chars), Hex sent data (4 chars)
15. The address is given as `82` in decimal form (as seen in the png file), so this becomes `52` in hex
16. 
