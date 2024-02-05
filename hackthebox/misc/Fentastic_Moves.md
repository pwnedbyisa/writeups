WIP
> Garry told me to catch some fish 20 meters deep<br>
#### Steps
1. There were no taskfiles to download, so I just started by navigating to the hosted site
```
Let's see if you can find the best moves for 25 puzzles! (Don't take too long tho :P)
White to Move (always)
Example: e2e4
[48;5;95m                    [0m
[48;5;95m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;95m [38;5;16m8[0m
[48;5;95m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;95m [38;5;16m7[0m
[48;5;95m  [48;5;7m [38;5;172mâ™”[48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m [38;5;172mâ™–[48;5;95m [38;5;16m6[0m
[48;5;95m  [48;5;240m  [48;5;7m [38;5;172mâ™˜[48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m [38;5;16mâ™œ[48;5;7m  [48;5;95m [38;5;16m5[0m
[48;5;95m  [48;5;7m  [48;5;240m [38;5;16mâ™œ[48;5;7m  [48;5;240m  [48;5;7m [38;5;16mâ™ž[48;5;240m  [48;5;7m  [48;5;240m  [48;5;95m [38;5;16m4[0m
[48;5;95m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;95m [38;5;16m3[0m
[48;5;95m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;7m  [48;5;240m  [48;5;95m [38;5;16m2[0m
[48;5;95m  [48;5;240m  [48;5;7m [38;5;16mâ™[48;5;240m [38;5;16mâ™š[48;5;7m  [48;5;240m [38;5;16mâ™›[48;5;7m  [48;5;240m  [48;5;7m  [48;5;95m [38;5;16m1[0m
[48;5;95m  [38;5;16m a b c d e f g h  [0m
What's the best move?
Wrong! Bye!
```
2. Ok so probably socket communication
3. I ran `nc IP PORT` (replacing them with the given instance) and ended up with a chess game in terminal
4. Now, my sister taught me how to play chess when she was in kindergaten (aka all the rules I know are wrong and the only person I've ever played and beaten is her) so this was looking a little rough
5. Anyways within the game if you take too long (around 3 seconds) to choose a move you lose, and it you chose the wrong move you lose
6. The game also says you have to complete 25 games to win manually
7. Obviously we will not be doing that
8. There is a slight delay between when the program starts and when the chessboard actually appears
```
Let's see if you can find the best moves for 25 puzzles! (Don't take too long tho :P)
White to Move (always)
Example: e2e4
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa                    
               ♚   8
       ♝     ♜     7
                 ♗ 6
   ♗   ♞           5
           ♟     ♞ 4
           ♔   ♖ ♛ 3
     ♙     ♖       2
                   1
   a b c d e f g h  
aaaWhat's the best move?
aaaaaaaa
Wrong! Bye!
```
and
```
Let's see if you can find the best moves for 25 puzzles! (Don't take too long tho :P)
White to Move (always)
Example: e2e4
ls                    
                 ♞ 8
         ♜       ♕ 7
         ♙         6
                 ♘ 5
         ♚ ♗ ♟   ♖ 4
     ♟     ♖     ♙ 3
                   2
             ♔     1
   a b c d e f g h  
 What's the best move?

Wrong! Bye!
```
9. Trying to run scripts during the delay also didnt work
10. I got pretty stuck at this point so i tried nmap
```shell
nmap -sV -Pn -p 57247 83.136.249.57 -v
```
Result:
```PORT      STATE SERVICE VERSION
57247/tcp open  unknown
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port57247-TCP:V=7.93%I=7%D=2/4%Time=65C00CD6%P=x86_64-pc-linux-gnu%r(NU
SF:LL,57B,"Let's\x20see\x20if\x20you\x20can\x20find\x20the\x20best\x20move
SF:s\x20for\x2025\x20puzzles!\x20\(Don't\x20take\x20too\x20long\x20tho\x20
SF::P\)\nWhite\x20to\x20Move\x20\(always\)\nExample:\x20e2e4\n\x1b\[48;5;9
SF:5m\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x1b\[0m\n\x1b\[48;5;95m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[4
SF:8;5;240m\x20\x20\x1b\[48;5;7m\x20\x1b\[38;5;16m\xe2\x99\x9c\x1b\[48;5;2
SF:40m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x2
SF:0\x1b\[38;5;16m\xe2\x99\x9d\x1b\[48;5;240m\x20\x20\x1b\[48;5;95m\x20\x1
SF:b\[38;5;16m8\x1b\[0m\n\x1b\[48;5;95m\x20\x20\x1b\[48;5;240m\x20\x20\x1b
SF:\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5
SF:;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\
SF:x20\x20\x1b\[48;5;95m\x20\x1b\[38;5;16m7\x1b\[0m\n\x1b\[48;5;95m\x20\x2
SF:0\x1b\[48;5;7m\x20\x1b\[38;5;172m\xe2\x99\x94\x1b\[48;5;240m\x20\x20\x1
SF:b\[48;5;7m\x20\x1b\[38;5;16m\xe2\x99\x9f\x1b\[48;5;240m\x20\x20\x1b\[48
SF:;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240
SF:m\x20\x20\x1b\[48;5;95m\x20\x1b\[38;5;16m6\x1b\[0m\n\x1b\[48;5;95m\x20\
SF:x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\
SF:x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[4
SF:8;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;95m\x20\x1b\[38;5;16m5\
SF:x1b\[0m\n\x1b\[48;5;95m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20
SF:\x20\x1b\[48;5;7m\x20\x1b\[38;5;172m\xe2\x99\x97\x1b\[48;5;240m\x20\x20
SF:\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[
SF:48;5;240m\x20\x20\x1b\[48;5;95m\x20\x1b\[38;5;16m4\x1b\[0m\n\x1b\[48;")
SF:%r(GenericLines,586,"Let's\x20see\x20if\x20you\x20can\x20find\x20the\x2
SF:0best\x20moves\x20for\x2025\x20puzzles!\x20\(Don't\x20take\x20too\x20lo
SF:ng\x20tho\x20:P\)\nWhite\x20to\x20Move\x20\(always\)\nExample:\x20e2e4\
SF:n\x1b\[48;5;95m\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\x20\x20\x1b\[0m\n\x1b\[48;5;95m\x20\x20\x1b\[48;5;7m\x
SF:20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x1b\[38;5;16m\xe2\x99\x9
SF:c\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1
SF:b\[48;5;7m\x20\x1b\[38;5;16m\xe2\x99\x9d\x1b\[48;5;240m\x20\x20\x1b\[48
SF:;5;95m\x20\x1b\[38;5;16m8\x1b\[0m\n\x1b\[48;5;95m\x20\x20\x1b\[48;5;240
SF:m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\
SF:x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\
SF:x1b\[48;5;7m\x20\x20\x1b\[48;5;95m\x20\x1b\[38;5;16m7\x1b\[0m\n\x1b\[48
SF:;5;95m\x20\x20\x1b\[48;5;7m\x20\x1b\[38;5;172m\xe2\x99\x94\x1b\[48;5;24
SF:0m\x20\x20\x1b\[48;5;7m\x20\x1b\[38;5;16m\xe2\x99\x9f\x1b\[48;5;240m\x2
SF:0\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\
SF:x1b\[48;5;240m\x20\x20\x1b\[48;5;95m\x20\x1b\[38;5;16m6\x1b\[0m\n\x1b\[
SF:48;5;95m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;
SF:240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x
SF:20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;95m\x20\x1
SF:b\[38;5;16m5\x1b\[0m\n\x1b\[48;5;95m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[
SF:48;5;240m\x20\x20\x1b\[48;5;7m\x20\x1b\[38;5;172m\xe2\x99\x97\x1b\[48;5
SF:;240m\x20\x20\x1b\[48;5;7m\x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;7m\
SF:x20\x20\x1b\[48;5;240m\x20\x20\x1b\[48;5;95m\x20\x1b\[38;5;16m4\x1b\[0m
SF:\n\x1b\[48;");
```
11. This is the game, but there appears to be other information besides colorcodes and instructions
12. I decided to hold on that and go back to the only other given information: the description and the title
13. Starting with the title, I found out that the FEN chess algorithm is a way to describe the way the board looks at any given point in game
14. The movement is long algebraic form (ex// e2e4), but we can't ignore the title
17. There was also this fix
> Fixed a logic bug in board generation, made answers consistent for different Stockfish versions and reduced challenge length.<br>
16. Which was essentially a hint that you need to use an outside chess engine (Stockfish) to solve the challenge
17. This lines up with the description; 20 levels deep means depth 20 on Stockfish
18. Garry chess = Garry Kasparov (literal inventor of chess)
19. Called in reinforcements atp because the skid blood runs strong but basically the script connected to the server, grabbed the netcat data, parsed it to be readable to stockfish, ran stockfish, parsed the output, found the correct move, and input the correct move to the netcat instance (all in python)
20. 4 hours deep and no luck (to be continued maybe)
