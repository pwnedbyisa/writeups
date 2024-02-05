WIP
> Did you know that racecar spelled backwards is racecar? Well, now that you know everything about racing, win this race and get the flag!
#### Steps
1. Unzip the file, password `hackthebox` (given)
2. We get an ELF executable for racecar
3. Connecting to the instance through netcat and playing a few times, I found that you wil always win if you choose car 1 and race 2 or vice versa
4. After winning, it allows for user input
5. Running strings on `racecar` there a couple interesting lines about flags, one just being flag.txt
6. 
