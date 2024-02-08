WIP
> Our infrastructure is under attack! The HMI interface went offline and we lost control of some critical PLCs in our ICS system. Moments after the attack started we managed to identify the target but did not have time to respond. The water storage facility's high/low sensors are corrupted thus setting the PLC into a halt state. We need to regain control and empty the water tank before it overflows. Our field operative has set a remote connection directly with the serial network of the system.
#### Steps
1. Unzip the file w password `hackthebox` (given) to get the `PLC_Ladder_Logic.pdf` and `interface_setup.png` files
2. Ladder logic is used to create software to run PLCs, where each device (such as a switch or relay) is represented on a series of horizontal rails to show logical progression
3. Here is the interface setup image
![interface_setup](https://github.com/pwnedbyisa/writeups/assets/138353745/1a83dc50-28bc-4907-a7cd-4437ecb8b0a5)
