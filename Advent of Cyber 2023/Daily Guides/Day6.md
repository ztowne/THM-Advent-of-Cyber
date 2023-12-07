# Day 6
## Memory Corruption
Today, we will be looking at some memory corruption issues and do a quick training sessison on memory corruption vulnerabilities. Toay'ds objectives include:

- Understand how specific languages may not handle memory safely
- Understand how variables might overflow into adjacent memory and corrupt it
- Exploit a simple buffer overflow to directly change memory you are not supposed to access

### The Game

The game we will be looking at today is called `CatOrMouse'. Our objective is to purchase a star for the Christmas tree. We can also buy ornaments to decorate the tree. In order to obtain money, we can use the in game computer to do online freelance programming jobs. 

We can also speak to 'Van Holly' to change our name for a fee of 1 coin per character.

![Alt text](/Advent%20of%20Cyber%202023/Resources/thegame.png)

### Is This a Bug?

In order to observe th bug, we are instructed to do the following:

 - Use the computer until we get 13 coins
 - Ask Van Holly to change our name to ```scroogerocks!```

After we do the above, we suddenly have 33 coins.

### Memory Corruption
 
 When we execute a program, all data will be processed through the computer's RAM. In this game, our coin count, inventory, position, movement speed, and direction are all stored somwhere in the memory and is updated as the game goes on.

 Good coding practice makes it so you each variable in memory can only be manipulated in ways the devloper intended. For example, our coin total should only change when we use the computer, or spend the coins. 

 Memory corruption vulnerabilites allow us to indirectly change the contents of the memory space in order to manipulate it to our desired value.

 ### Debugging the Game

The developers of this game have added a debug panel to watch the memory layout of the gam'es variables. We can access this by pressing ```TAB```.

![Alt text](/Advent%20of%20Cyber%202023/Resources/debugpanel.png)

We can also cycle back and forth between the Hex version and the ASCII version of the debug panel

### Investigating the "scroogerocks!" Case

When we access the PC, we can see the coins increase in the debug panel. We notice right before the coins memory space, we can see the ```player_name``` variable. We also noticed the ```player_name``` variable can only accomodate 12 bytes of information. Therefore, if we enter a name that has more than 13 characters (or 13 bytes) it will overflow into the coins variable. To demonstrare this, we change our name to ```aaaabbbbccccx```. 

Our coin value changes from 13 to 120 coins. The ASCII version of the debug panel shows the ```x``` overflowed into the first byte of the coins variable. The ASCII hexadecimal value for ```x``` is ```0x78``` which is ```120``` in decimal representation.

What we just did is a very simple buffer overflow, adn cna be used to corrupt memoery right next to the vulnerable variable.

### Strings in More Detail

When strings are writted in memory, each character is writted in order, taking 1 byte each. A NULL character is simply a byte with the value ```0x00```. When reading a variable as a string, the game will stop at the first NULL character it finds. Any character after NULL is ignored. To demonstrate this, we change our name to ```AAAABBBBCCCCDDDD```. 

![Alt text](/Advent%20of%20Cyber%202023/Resources/nullexample.png)

We noticed that the ```shopk_name``` has a NULL first byte. The game adds a NULL character after the 16 bytes in our name, which overwrites the ```shopk_name``` variable. When talking to the shopkeep, we see his name is blank. This is because C++ doesn't have check boundaried, therefore it reads our name from the start of the ```player_name``` variable until it reaches the first NULL byte.

### Integers and the Coins Variable

If we enter our name as ```AAAABBBBCCCCDEFG``` we notice our coins equals ```1195787588```. We notice in the HEX debug panel, the coin variable says ```44 45 46 47 ```We are then instructed to use an online tool to convert this from HEX to decimal. 

![Alt text](/Advent%20of%20Cyber%202023/Resources/stringvariable.png)

However, C++ stores variables in a different way. First, intergers have a fixed memory space of 4 bytes. Second, an integer's bytes are stored in reverse order - know as the ```little-endian byte order```.

If we take our current coin count of ```1195787588``` and convert it ffrom number to hex, we get ```47464544``` which is the reverse order of what's shown in our debug panel but backwards.

![Alt text](/Advent%20of%20Cyber%202023/Resources/debugpanelhex.png)

### Winning the Game

In order to win the game, we must get ```$10000``` to buy the star. However, the shopkeep will not sell us the star, accusing us of cheating. However, we can use the id of the item shown in the shop to exploit the buffer overflow again. 

![Alt text](/Advent%20of%20Cyber%202023/Resources/starid.png)

All we have to do, is change our name to in order to get a ```d``` in one of our inventory bytes. After we change our name, we can see our inventory has (more than one) stars.

![Alt text](/Advent%20of%20Cyber%202023/Resources/winninggame.png)

After we put the star on the tree, we get our flag for today's challenge

```THM{mchoneybell_is_the_real_star}```

We also must answer the following question:

```If the coins variable had the in-memory value of "4f 4f 50 53", how many coins would we have in game?```

First, we must figure out what these mean in hex. We use a simple ```Hex to Text``` converter to figure out this means ```OOPS```:

![Alt text](/Advent%20of%20Cyber%202023/Resources/hextotext.png)

In the game, we enter the following to execute a buffer overflow attack, and get our final answer:

```AAAAAAAAAAAAOOPS```

This takes up the first 12 bytes of the ```player_name``` variable and allows us to manipulate the ```coins``` variable with the last 4 bytes. We get our answer of ```1397772111```.

## Conclusion
Today, we explored buffer overflows and how we can manipulate them to achieve our desired outcome. We also learned about different programming languages and the way they store variables in memory.

