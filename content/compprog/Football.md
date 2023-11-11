+++
title = 'Football'
date = 2023-10-02T21:39:21-05:00
draft = false
+++
The International one, not the American one.
<!--more-->
## Introduction


### Problem Statement
![failed](/images/compprog/football/ProblemStatement.png)

So basically, we have a bunch of 1's and 0's. We need to find a substring where
there is at least 7 consecutive 1's or 0's.

![failed](/images/compprog/football/input1.png)

In this example, since at no point in the string is there 7 1's in a row, or 
7 0's in a row, we print out the word "NO".

### Concept

To solve this problem, we can just iterate over the string and check to see if
the current character we are on matches the previous character. If it does, we
will update a counter variable that has been initialized to 1. If we don't match
the previous character, we reset the counter to 1.

## Implementation
Now we start off with our boilerplate.

    fn main() {
        let mut buffer = String::new();
        stdin().read_line(&mut buffer);
    
    }

Here we are just handling the input.
Now for parsing the input.

    let vector: &[u8] = buffer.trim().as_bytes();
    let length = vector.len();

Here we initialize two variables. One is going to hold our array of characters,
while the other will hold the length of the array.

We can then move on to our loop.

    let mut x = 1;
    let mut count = 1;
    while count < 7 && x < length {
        if vector[x] == vector[x-1] {
            count += 1;
            x += 1;
        } else {
            count = 1;
            x += 1;
        }
    }

Here we initialize two more variables: 'count' and 'x'. We then create a while
loop that will run as long as count is less than 7 AND x is less than the length.

In our next conditional, we check and see if the current character is the same
as the previous character. If it is, we increment the counter variable, as well
as increment 'x' to move on to the next character.

If the characters don't match, we reset the counter back to 1 and move on to 
the next character.

When the loop terminates, we move on to our final conditional.

    if count > 6 {
        println!("YES");
    } else {
        println!("NO");
    }

If count is greater than 6, we print "YES". If it isn't we print "NO".

And that sums up our program to complete this challenge. Here is the full code
solution.

### Full Source Code
    use std::io::stdin;
    use std::cmp::max;
    
    fn main() {
        let mut buffer = String::new();
        stdin().read_line(&mut buffer);
    
        let vector: &[u8] = buffer.trim().as_bytes();
        let length = vector.len();
        
        let mut x = 1;
        let mut count = 1;
        while count < 7 && x < length {
            if vector[x] == vector[x-1] {
                count += 1;
                x += 1;
            } else {
                count = 1;
                x += 1;
            }
        }
    
        if count > 6 {
            println!("YES");
        } else {
            println!("NO");
        }
    }
