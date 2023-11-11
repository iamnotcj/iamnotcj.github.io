+++
title = 'Beautiful Matrix'
date = 2023-09-15T03:19:14-05:00
draft = true
featured = false
+++
Wake up, Neo.
<!--more-->
## Introduction
### Problem Statement
![failed](/images/compprog/beautifulmatrix/ProblemStatement.png)

So basically we are given a 5x5 matrix of 0s. On a certain coordinate (x,y) we have a single 1. 
Now the goal of this problem is to find a way to move the 1 to the center of the matrix, because a 1 in 
the middle of the matrix makes it *beautiful*.

![failed](/images/compprog/beautifulmatrix/input2.png)

Since we will always get a 5x5 matrix, we know that the middle of the matrix is point (3,3). So we can walk from
wherever '1' is to (3,3) and the amount of moves we do will be our answer. 

Here for instance, we have our '1' value at (2,3). To get to (3,3) we move 1 to the right. Hence our answer of one move.
### Concept
![failed](/images/compprog/beautifulmatrix/input1.png)

Let's look at another! Here we see that '1' is at (5, 4). We can make a formula to quickly give us the amount of moves to the center.
Our forumla can be simply subtracting the coordinate by the middle (3,3). This will give us a result of (2, 1), and then from there we can take the absolutes of these coordinates and add them together to get our total moves:

3 = |2| + |-1|

## Implementation

So now we just have to code our logic out, we can first start with initializing our buffer and a tuple that will hold the location of the lone '1' value in the matrix.

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        let mut coord = (0, 0);

        Ok(())
    }

Next we will handle how we recieve the matrix from the input. I opted to just read each row of the matrix in a 
loop. There are 5 rows so we loop 5 times.
        
    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        let mut coord = (0, 0);

    //  #1
        for x in 1..=5 as i32 {
            buffer.clear();
            stdin().read_line(&mut buffer)?;
        }

        Ok(())
    }


This should allow use to read each row of the matrix. Diving more into the declaration of the loop at #1, we are
iterating over an inclusive range (1..=5). This means that 'x' will start at 1 then loop all the way to 5. This allows us to conceptualize the 'x' variable in this loop as the current place on the x-axis we are currently on.

We can now implement our logic to actually search for the '1' value on each row! We can do this buy creating 
another loop to iterate over each y-coordinate of the row we are currently on. So say we are on row 2, we can loop over (2,1), (2,2), (2,3), etc.

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        let mut coord = (0, 0);

        for x in 1..=5 as i32 {
            buffer.clear();
            stdin().read_line(&mut buffer)?;
            
    //      #2
            buffer.trim()
                  .split(" ")
                  .enumerate()
                  .for_each(|(y, value)| if value == "1" { 
                                            coord = (x,(y + 1) as i32); 
                                         });
        }

        Ok(())
    }

Let's break #2 down. So we start off with our buffer\.\.\.

We call "trim()" to shave off the newline character.

We call "split()" to split each character of our string by whitespaces and give us an iterator to iterate over.

We call "enumerate()" to match an index value which each element we iterate over. So now instead of iterating over something like [ 0, 0, 0, 1, 0 ] in a row, we will iterate over (index, value) i.e. [ (0, 0), (1, 0), (2, 0) \.\.\.]. We can use this to our leverage by conceptualizing the "index" as the current y-coordinate of the row and the value being if its a 0 or a 1.

We call "for_each()" to create a loop over this iterator, and we can supply a closure to perform some work on each object "(y, value)" we recieve. The work we will be doing is\.\.\.
    
    if value == "1" {
        coord = (x, (y + 1) as i32);
    }

Here we just update the coord with the "x" variable of the outer loop, and the index/y of the iterator. Note
that we need to add 1, because the index actually started from 0, rather than 1. We put that operation in paranthesis "(y + 1)" so we can than "cast" it as an i32 i.e. a 32-bit integer.

And now we are almost done! Now we just need to subtract our coord object by the middle and sum up the absolute values. We can do this by\.\.\.

    use std::io::stdin;
    use std::io::Error;

    fn main() -> Result<(), Error> {
    //  <-- snip -->

    //  #1 
        coord.0 = coord.0 - 3;
        coord.1 = coord.1 - 3;

    //  #2
        println!("{}", (coord.0).abs() + (coord.1).abs());

        Ok(())
    }

At #1, we just subtract 3 from each coordinate in our "coord" object.

#2 is where we actually print the results to the screen. Now inside of the println! function is where we actually
perform the summation by calling the absolute() function on each coordinate of our "coord" object first before
adding.

### Full Code Solution
    
    use std::io::stdin;
    use std::io::Error;
    
    
    fn main() -> Result<(), Error> {
        let mut buffer = String::new();
        let mut coord = (0, 0);
    
        for x in 1..=5 as i32 {
            buffer.clear();
            stdin().read_line(&mut buffer)?;
    
            buffer.trim()
                  .split(" ")
                  .enumerate()
                  .for_each(|(y, value)| if value == "1" { 
                                            coord = (x,(y + 1) as i32); 
                                         });
        }
    
        coord.0 = coord.0 - 3;
        coord.1 = coord.1 - 3;
    
        println!("{}", (coord.0).abs() + (coord.1).abs());

        Ok(())
    }


    use std::io::stdin;
    use std::io::Error;
    
    
    fn main() -> Result<(), Error> {
    //  <--- snip ---> 

        coord.0 = coord.0 - 3;
        coord.1 = coord.1 - 3;

    //  <--- snip ---> 
    }

