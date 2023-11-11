+++
title = 'Example 1'
date = 2023-09-15T03:19:14-05:00
draft = false
featured = false
+++
Lets apply some of the stuff that we learned to this challenge!
<!--more-->
## First Run
![failed](/images/expdev/re/example1/run1.png)

So when we first run the program, we are given a prompt for our name.

![failed](/images/expdev/re/example1/failname.png)

Upon giving it a name ("bruh"), it tells us that we supplied an unrecognized 
name and terminates.

## Valid Name Check
![failed](/images/expdev/re/example1/main.png)

We didn't really get too far in this program, so let's take a look at the 
disassembly. 

Presented above is the disassembly of the main function.

![failed](/images/expdev/re/example1/foundvalname.png)

After scrolling through the disassembly, we reach a function called 
"validate\_name" 

![failed](/images/expdev/re/example1/foundAlice.png)

Looking at the disassembly of this function, we see that a call to "strcmp" is
made against the name Alice. This can be a clue for how we can get further in
the program.

![failed](/images/expdev/re/example1/returnName.png)

After the comparison is made, we make a jump if the strings are equal to an 
address that will move "1" to the eax register

Another jump occurs after the move instruction, to the end of the program.

So if the function is successful (we give a valid name) we return with 1. What
do we do with this 1?

![failed](/images/expdev/re/example1/mainpassfirst.png)

If we jump back to the main function, we see another jmp conditional right 
after we return. It depends on the results of the "test eax" instruction before
it.

The results of the test instruction if eax is 1, should statify the jump 
condition.
## Valid Serial Check
![failed](/images/expdev/re/example1/foundvalserial.png)

Now that we have theoretically gotten past the first obstacle in the program,
we can move on to the second part of the program. Here we need to input a 
serial.

![failed](/images/expdev/re/example1/algorithm.png)

Looking at the disassembly, we can see map out some of the local variables at
the start of the function to help us in figuring out what is going on.

Looking past the variables we can see an algorithm that produces the serial 
code for the program. We see some interesting stuff like division and 
operations on the length of the string we gave in the earlier part of the 
program.

This loop runs for 0x4ff which means that it loop more than 1000 times.

A solution could be studying this algorithm, looking at the inner workings of
how everything works, and meticulously replicating it ourselves in a 
programming language of our choosing.

## Serial Code Leak
![failed](/images/expdev/re/example1/imgoodbruh.png)

Yeah, I 'aint doing 'allat\.\.\.

![failed](/images/expdev/re/example1/cmp.png)

This algorithm doesn't change much each time we run the program, so instead of
doing all of that we could just harvest the code after the algorithm finishes 
doing its thing.

![failed](/images/expdev/re/example1/breakpoint1.png)

We can set up a breakpoint at the address right after the loop terminates. 
From here, we will be able to check out the two variables being compared with
at the end of the function.

![failed](/images/expdev/re/example1/leakserial.png)

Here we see both our input ($rbp-0x2c) and the local variable from before
(rbp-0x18). These are the values that are being compared with at the end of 
the function.

![failed](/images/expdev/re/example1/printdecimal.png)

We can then get the decimal form of the local variable that our input is being
compared with and get the number that should satisfy the conditional at the end
of the function.

## Script
![failed](/images/expdev/re/example1/script.png)

We then write a script to automate the process of supplying the program with
the name "Alice" and entering the serial number that we just leaked.

![failed](/images/expdev/re/example1/flag.png)

And after running the script, we get our flag!

