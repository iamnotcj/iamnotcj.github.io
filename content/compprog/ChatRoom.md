+++
title = 'Chat Room'
date = 2023-10-04T20:40:08-05:00
draft = true
+++
Do people still use skype? Asking for a friend.
<!--more-->
## Introduction
### Problem Statement
![failed](/images/compprog/chatroom/ProblemStatement.png)

So this guy makes a lot of typos (since he just learned to type). We are
asked to create a tool that will convert whatever jumble of characters he types
into the world "hello", if and only if, we can delete some letters to reveal the
word "hello".

![failed](/images/compprog/chatroom/input1.png)

As you can see here, we can delete one 'h', one 'a', three 'l's, one 'o', and 
one 'u'. After all of our deletions, we are left with the string "hello"

### Concept

We can initialize an array with the letters of the word "hello" and while we
traverse the input, mark off the letters we encounter in order.

## Implementation

First we need to handle the input, as well as initalize our array of letters.

    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
        let word: [char; 5] = ['h', 'e','l', 'l', 'o'];
        
        let _ = stdin().read_line(&mut buffer);
        
        Ok(())
    
    }

Here we create a 5 character array that will spell out the word "hello". We will
use this as a way of striking off characters we encounter in the input that we
are handling.

We don't need to parse anything, so we can go straight into the logic of our 
program.

        let mut index = 0;
        for character in buffer.trim().chars() {
            if index < 5 && character == word[index] {
                index += 1;
            }
        }

We are iterating over every character that was supplied by the input. We then
reach a conditional that checks to see if we haven't reached the end of our
5 character array. 

The second portion of the conditional checks to see if the
character of the input equals the current character of the "hello" array we are
indexing. If we are, we strike out the current character in the "hello" array,
by incrementing the index.

Let's go through an example,

``` 
    input = hhhellloooa
    array = [h, e, l, l, o]
    index = 0

    Iteration #1
        Since the first character of input equals the first character of the 
        array,
            Array = [e, l, l, o ]
            index = 1

        Since we haven't reached the end of the array, we loop back again.

    Iteration #2
        Since the current character of the input doesn't equal 'e' in the 
        array, we do nothing.
            Array = [e, l, l, o]
            index = 1

        Since the we haven't reached the end of the array, we loop back again.

    Iteration #3
        Same as iteration #2

    Iteration #4
        Since the current character of the input does equal 'e' in the array,
            Array = [l, l, o]
            index = 2

        Since we haven't reached the end of the array, we loop back again.

    Iteration #8
        Since the current character fo the input does equal 'o' in the array,
            Array = []
            index = 5

        Since we have reached the end of the array, we stop.
```

Now if the index is greater than the length of the array from the beginning 
of the function, we know that we can create the string "hello" from the input.

        if index > 4 {
            println!("YES");
        } else {
            println!("NO");
        }

And that is the program! Here is the full code solution.

                
### Full Code Solution
    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
        let word: [char; 5] = ['h', 'e','l', 'l', 'o' ];
        
        let _ = stdin().read_line(&mut buffer);
        
        let mut index = 0;
        for character in buffer.trim().chars() {
            if index < 5 && character == word[index] {
                index += 1;
            }
        }
    
        if index > 4 {
            println!("YES");
        } else {
            println!("NO");
        }
            
        Ok(())
    
    }
                
