+++
title = 'FindMyPassword.exe'
date = 2022-03-20T13:51:01-05:00
draft = false
+++
This is a older article where I solve a crackme challenge.
<!--more-->
![failed](/images/Miscellaneous/findmypass/introphoto.png)

Another Reverse Engineering walk-through. Lets get straight into it!

![failed](/images/Miscellaneous/findmypass/photo1.png)

Here we see that it requires an argument for the execution of the program.

![failed](/images/Miscellaneous/findmypass/photo2.png)

After supplying an argument, it told me that I had the wrong password. 

With nothing else to go on, I thought this was a good enough stopping point 
to move on to the disassembly.

![failed](/images/Miscellaneous/findmypass/photo3.png)

Upon opening IDA Pro, we landed right in main and we even get to see 
one of the strings. Great! 

There are a couple calls to “string compare” in the function, so I spent the 
next portion of my time trying to figure out what it was comparing.

![failed](/images/Miscellaneous/findmypass/photo4.png)

Thankfully, I didn't have to look far to see what appears to be a hashed string. 
The labels in IDA seem to point to it being a hashed string or password. 
As you can see, there is also a hashing function right above it. 

This string seemed to be what the program would compare our argument too after 
our argument itself was hashed.

![failed](/images/Miscellaneous/findmypass/photo5.png)
Upon analyzing the disassembly of the hash function, I came to the following 
analysis:

There was memory being allocated at the start of the function, with the size 
being the length of our argument multiplied by 4 (it does this by shifting 
left 2 bits). 

The function then created a for loop which iterated over each character of our 
argument and hashed it by plugging it in the following formula:

our\_letter + allocated\_number + (current\_iteration * 4)

![failed](/images/Miscellaneous/findmypass/photo6.png)

Next, we hop over to the debugger. I figured that the computed sequence of 
numbers had to be in memory somewhere before the final addition instruction 
that would hash the character. Sure enough, it was. 

After playing around with the size of the argument, I got a couple numbers that 
were used in the operation.

00, 02, 05, 09, 01, 01, 08, 08, 03, 06, 02, 04, 07 (not shown)

These numbers represent this part of the formula:

[allocated\_number + (current\_iteration * 4)]

![failed](/images/Miscellaneous/findmypass/photo7.png)

I then wrote a simple python script and after messing around with certain 
instructions, created this to de-hash the password we saw earlier.

![failed](/images/Miscellaneous/findmypass/photo8.png)

After running the script we now have a string with some noise to try

![failed](/images/Miscellaneous/findmypass/photo9.png)

Sure enough, it works! This challenge took a while to finish, but I had a 
lot of fun completing it.

