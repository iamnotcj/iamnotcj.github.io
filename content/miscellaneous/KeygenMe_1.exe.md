+++
title = 'KeygenMe_1.exe'
date = 2022-03-15T13:51:10-05:00
draft = false
+++
This is a older article where I solve a crackme challenge.
<!--more-->

![failed](/images/Miscellaneous/Keygenme/introphoto.png)

 I found this crackme on crackmes.one, and I figured I’d take a *crack* at it.

![failed](/images/Miscellaneous/Keygenme/photo1.png)

We can see here that on first entering any input it requires us to use a valid format.

![failed](/images/Miscellaneous/Keygenme/photo2.png)

Upon entering a valid format, we can see that I have reached the programs first 
check in which I have failed. Perhaps I need to use numbers instead?

![failed](/images/Miscellaneous/Keygenme/photo3.png)

Upon using numbers instead of letters, I ended up reaching the 2nd fail safe. 
At this point I thought that I had enough information to start the disassembly.

![failed](/images/Miscellaneous/Keygenme/photo4.png)

I first searched for the strings from the program, I started off with the 
“Invalid key” string in hopes of tracking down the function that it resides in.

![failed](/images/Miscellaneous/Keygenme/photo5.png)

Instead, I found it to be apart of a condition statement somewhere in main. 
I then saw a familiar function (thunk_FUN_004070c0) a couple lines after it 
that would print out the opposite string (“Key in correct format”) and renamed 
it to represent that.

![failed](/images/Miscellaneous/Keygenme/photo6.png)

Next I looked for the “Check number” strings to see where their residing 
functions were. I first started off with Check One and followed the external 
references to its primary function and renamed it.

![failed](/images/Miscellaneous/Keygenme/photo7.png)

After reading the disassembly, I came to a possible conclusion that the way 
Check One worked was by checking the first 4 digits of the string and making 
sure that each digit is between the numbers 0 and 9. 

It does this by creating a for loop with a nested if condition. It takes the 
address of the string and adds 1 to it before de-referencing to the 
corresponding “byte” and ensuring that it is greater than 0, but less than 9. 

If either of the first 4 numbers fails this check, a local variable (local_c) 
is decreased to 0 signaling that the string didn’t pass the check. Th
e function then continues on with the final conditional check which verifies 
if this variable has been changed to 0. 

If it has, it prints out that you have failed the first check.

![failed](/images/Miscellaneous/Keygenme/photo8.png)

I did the same process of following external references to find the Check Two 
function as well. 

After reading the disassembly I came to a possible conclusion that this followed 
the same template as the first check, with the only difference being that now 
it was checking for the next 4 numbers being even. 

It accomplishes this by taking each digit and performing an AND binary operation 
with 1. If the result of that does not equal 0. The same local variable from 
before will change to 0 and the same check will perform at the end to see if 
it is 0. 

If it is, the error statement will print out just like in the first one.

![failed](/images/Miscellaneous/Keygenme/photo9.png)

To the final check, to my surprise it wasn't a copy of first two. After reading 
the disassembly it appears that it checks only for the first digit of the last 
4 in the string. It checks to see if it contains the letter R. 
I wonder if there is actually supposed to be more to this function than what
is shown. 

If we look at the top, there are a couple of local variables that are 
initialized to certain characters at the beginning of the function. After 
translating them from hex it reads out the word "R3KT". 

Was there supposed to be some check for the last 4 characters of our license key 
to ensure that it equals “R3KT”? I’m not sure, but what I am sure of is that 
it checks for ‘R’ at least.

![failed](/images/Miscellaneous/Keygenme/photo10.png)

Knowing what we know now, I restarted the program and entered in a new key following the new information that I had just obtained. After making sure that the first 4 symbols were numbers, the next 4 were even, and that the last 4 had to start with the letter 'R', we are able to finally crack this program.
