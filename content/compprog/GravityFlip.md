+++
title = 'Gravity Flip'
date = 2023-10-03T17:39:55-05:00
draft = true
+++
I landed this at a skatepark once\.\.\.
<!--more-->
\.\.\.in a video game. But that's not important!
## Introduction

### Problem Statement

![failed](/images/compprog/gravityflip/ProblemStatement.png)

So we need to take the stacks of cubes in the toy box and when the gravity 
"shifts" to the right, we count the results.
![failed](/images/compprog/gravityflip/input1.png)

Exhibit A, when the gravity shifts to the right we are left with [1, 2, 2, 3].

### Concept

There are way too many words in these instruction, just to hide the fact that
all we need to do is sort the array.

## Implementation

We can start with handling the input.

    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
    
        let _ = stdin().read_line(&mut buffer);
        buffer.clear();

        let _ = stdin().read_line(&mut buffer);
    
    
        Ok(())
    }

We don't need the first line of the input which will denote the length of the 
array. We do need the second line which holds the array in a string.

After getting the second line, we need to parse the string into a vector of 
integers. We can do so like this.

        let mut result = buffer.trim()
                               .split(" ")
                               .map(|x| x.parse::<i32>().unwrap())
                               .collect::<Vec<i32>>();
    
Here we chain a couple of methods together to create our Vector of integers.

Trim(): This allows us to remove the newline character at the end of the buffer

Split(): This allows us to create an iterator by splitting the buffer by its
whitespace.

Map(): This allows us to consume the previous iterator and create a new 
iterator, whose elements are the parsed elements of buffer.

Collect(): This allows us to consume the previous iterator and create our
Vector of integers.

Now that our vector is created, all we need to do is sort it. It's as simple as
this.

        result.sort();
    
And we are done! All we have to do now is print the elements of the vector and
we have completed this challenge. Here is the full code solution.
    
### Full Code Solution

    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
    
        let _ = stdin().read_line(&mut buffer);
        buffer.clear();
        let _ = stdin().read_line(&mut buffer);
    
        let mut result = buffer.trim()
                               .split(" ")
                               .map(|x| x.parse::<i32>().unwrap())
                               .collect::<Vec<i32>>();
    
        result.sort();
    
        for x in result {
            print!("{} ", x);
        }
    
    
        Ok(())
    }
