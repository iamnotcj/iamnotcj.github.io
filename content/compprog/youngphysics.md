+++
title = 'Young Physicist'
date = 2023-09-15T03:19:14-05:00
draft = false
featured = false
+++
Insert Oppenheimer reference here.
<!--more-->
## Introduction

### Problem Statement   
![failed](/images/compprog/youngphysicist/ProblemStatement.jpg)

So basically, a student procrastinates and forgot to do their homework. The next day, their teacher gives them an opportunity to make
it up by completeing a physics problem.   


It's a Physics problem, and he has to calculate if all the forces interacting with a material point is in equilibrium.

![failed](/images/compprog/youngphysicist/input1.png)
In this example, he is given three force vectors, and to calculate the equilibrium he would need to sum up all of the vectors with each other.

To do this he would need to take the 1st X-coordinate, then add it to the 2nd X-coordinate, then add that to the 3rd X-coordinate. Then he would need to repeat that 
same process for the other coordinates.

If at the end of this process he gets (0, 0, 0), he has found the equilibrium!
### Concept
![failed](/images/compprog/youngphysicist/input2.png)
Let's work this one out. If we add up all the X's ( 3 + -5 + 2 ), we get 0.

If we add up all the Y's ( -1 + 2 + 1 ), we get 0.

If we add up all the Z's ( 7 + -4 + -3 ), we get 0.

Hence, we get a final vector of ( 0, 0, 0 ). An equilibrium!

## Implementation
Ok, so let's get started! I will be using Rust for this problem. First, we need to write all of our boilerplate code.
    
    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
        Ok(())
    }

Now we need to initialize a buffer to read in our input, we can do that buy:

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        
        Ok(())
    }

We should also initalize a vector to help sum up all of our coordinates:

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        let mut sum: Vec<i32> = vec![0,0,0];
        
        Ok(())
    }

And now we are ready to recieve input! Now we just need to find a way to handle the data coming in.
If we recall back to the examples, the first line sent will tell us how many vectors will be calculated.
We can use the information from the first line as an upperbound for a loop where we continue to read lines from 
successive inputs.

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        let mut sum: Vec<i32> = vec![0,0,0];
        
        //#1
        stdin().read_line(&mut buffer)?;

        //#2
        let count = buffer.trim()
                          .parse::<i32>()
                          .unwrap();

        //#3
        for _x in 0..count {

        }

        Ok(())
    }

Let's break this one down! So at number 1, we are simply reading a line to that buffer variable we just
created. We are passing a "mutable reference" to the "read_line" function. This basically means we are allowing 
the function to "borrow" the buffer and perform some work on it. The question mark at the end is not important right now.
Just know that it's there for error handling purposes.

Moving on to #2, we then can take that buffer (which is initialized as a String) and perform operations to convert 
it to an integer to use in our loop. 

Trim shaves off the newline character of the line. 

Parse does the actual converting. 

Unwrap is interesting if you haven't used Rust before. The previous function doesn't actually return the
Integer directly. Instead, it returns a struct called 'Result'. This is one of the many error handling features of
Rust. To actually get the integer we need to "unwrap" the struct and return the "item" of said structure, in this case our Integer.

Moving on to #3, this is our for loop! We have denoted our variable 'x' (the "\_" before it is a way of saying we 
aren't going to use this object at all) and our range "0..count".

So now let's start writing the logic for our loop! Let's handle the input first. We are going to reuse the same 
buffer that we created at the start of the main function. To do this we need to clear it. We don't need the old
contents of it on each iteration, so this is safe to do.

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        let mut sum: Vec<i32> = vec![0,0,0];
        
        stdin().read_line(&mut buffer)?;

        let count = buffer.trim()
                          .parse::<i32>()
                          .unwrap();

        for _x in 0..count {
            buffer.clear();
            stdin().read_line(&mut buffer)?;

        }

        Ok(())
    }
    
So now the logic part. We have a vector already called "sum" and now we need to read in the force vectors
from the input and add them to each coordinate of our vector. We know that all inputs will come in as (x, y, z)
and our 'sum' vector is layed out as sum[0] = x, sum[1] = y, sum[2] = z. So what we could do is for each number we
recieve in as input, add to that number to sum vector at its current index then increment that same index.

Let's break that down. So we recieve an input i.e. -1, 2, -3. We can loop over each number and add it to our sum vector. So first we initalize "index = 0", we take -1 on our first iteration and add it to sum[index]. From there, we increment our index so that on the next iteration we will be adding '2' to sum[index] \(which is actually sum[1]), which as stated before, is our Y-Coordinate.

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {

    //  --- snip ---
        for _x in 0..count {
            buffer.clear();
            stdin().read_line(&mut buffer)?;

            let mut index = 0;
            buffer.trim()
                  .split(" ")                                     //#1
                  .map(|i| i.parse::<i32>().unwrap())             //#2
                  .for_each(|j| { sum[index] += j; index += 1; }) //#3
        }

        Ok(())
    }

At #1, we have the split function, which will split our string by the whitespaces and turn it into an iterator we 
can "iterate" (nice) over. 

At #2, we have the map function. Now with this we can supply a closure (you can think of this as a quick function) that will modify each element of the split iterator from before. ("Modify" is technically the wrong word as it "consumes" the split iterator and returns its own iterator, but that's not important for this analogy. The concept is the same)

At #3, we have the for_each function. This is just a cleaner way of calling a for loop after chaining so many functions together. Here we can supply another closure where we can implement the summation and increment logic we brainstormed before.

Now the only thing we have left is to verify that we actually have an equilibrium! There are a couple of ways to do this, I thought it would be cool if we just added up all of the absolute values of the elements and if they equaled 0 then you had an equilibrium. 

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {

    //  --- snip ---
    
        if sum.iter().map(|x| x.abs()).sum::<i32>() == 0 {
            println!("YES");
        } else {
            println!("NO");
        }

        Ok(())
    }

It's probably (definitely?) faster to just iterate over each element and if it's not 0 then print no. 

And that's everything! The student can now procrastinate even more in class.

### Full Code Solution

    use std::io::stdin;
    use std::io::Error;
    
    
    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        let mut sum: Vec<i32> = vec![0,0,0];
    
        stdin().read_line(&mut buffer)?;
    
        let count = buffer.trim()
                          .parse::<i32>()
                          .unwrap();
    
        for _x in 0..count {
            buffer.clear();
            stdin().read_line(&mut buffer)?;

            let mut index = 0;
            buffer.trim()
                  .split(" ")
                  .map(|i| i.parse::<i32>().unwrap())
                  .for_each(|j| { sum[index] += j; index += 1;})
        }
    
        if sum.iter().map(|x| x.abs()).sum::<i32>() == 0 {
            println!("YES");
        } else {
            println!("NO");
        }
        Ok(())
    }

