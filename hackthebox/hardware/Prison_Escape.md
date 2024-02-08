WIP
> Our agent was caught during an audacious espionage mission, and is being held in a high security prison protected by advanced alarm systems and laser technology. Prior reconnaissance of an adjacent landfill site revealed a prison blueprint and technology document. Our exfiltration team is ready at the emergency exit. The agent is skilled at lock picking, but any attempted escape would trigger the alarm system and a prison-wide lockdown. Can you disable the security controls and help the agent escape?
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the `blueprint.png` and `Omega.pdf` files
2. The blueprint contained info about the location of motion sensors, laser, alarms, guards, cells, etc 
3. The pdf contained information about hardware functionality, architecture, and the alarm and laser packet structure for alarm alerts, suppression/silencing, broadcasting, and laser movement
4. Navigating to the web instance, we get this page:
![image](https://github.com/pwnedbyisa/writeups/assets/138353745/8319b1c9-d7ba-46ef-88f6-e13029f4d0ce)
5. I started by downloading the drone live capture `.complex` file for analysis
6. 

