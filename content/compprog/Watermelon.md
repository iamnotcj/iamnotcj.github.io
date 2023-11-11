+++
title = 'Watermelon'
date = 2023-09-11T15:36:56-05:00
draft = true
+++
I've recently learned about Competitive Programming and started solving 
problems\.\.\. 
<!--more-->
I hope this isn't just a phase\.
## Introduction
### Problem Statement
![failed](/images/compprog/Watermelon/ProblemStatement.png)

So Pete and his friend Billy have various watermelons and would like to know if
it's possible to evenly divide the watermelon into 2 even numbers.

![failed](/images/compprog/Watermelon/input1.png)

So in this example, the answer would be yes because you can divide the 8 kilo 
watermelon into a part that is 2 kilos and another that is 6 kilos.

### Concept

Since the only stipulation is that both parts need to be even numbers, and every
even number is divisible by 2. We could just determine if the original 
watermelon is even.

## Implementation

We can start with some boilerplate where we recieve input\.\.\.

    use std::io::stdin;

    fn main() -> Result<(), String> {
        let mut input = String::new();
        
        let _ = stdin().read_line(&mut input);
    
        Ok(())
    }
              
        
Now we just need to get parse the input into a usable type\.\.\.

    use std::io::stdin;

    fn main() -> Result<(), String> {
        let mut input = String::new();
        
        let _ = stdin().read_line(&mut input);
        // #1
        let number =  input.trim()
                           .parse::<i32>()
                           .unwrap();
    
        Ok(())
    }

Let's break this down. So we take the input and assign it to the number 
variable, but before then we\.\.\.

"Trim()": We remove the newline character from the input string. 

"Parse()": We then take the input string and parse it to a 32 bit integer.

"Unwrap()": The result of the parsing produces the value we want, but it's 
wrapped in a "Error-handling object" (for a lack of a better term). We must call
this function to unwrap the object and retrieve the value we want.

So now we move on to the logic of the program. Here, we can use conditionals.
If the number is even, then print "YES". If the number is not even, then print
"NO".

We can implement this like\.\.\.

    use std::io::stdin;

    fn main() -> Result<(), String> {
    //  <--snip-->
        if number % 2 == 0 { println!("YES"); }
        else { println!("NO"); }
    //  <--snip-->
    }

And that's pretty much it!

### Full Code Solution

    use std::io::stdin;

    fn main() -> Result<(), String> {
        let mut input = String::new();
        
        let _ = stdin().read_line(&mut input);
        
        let number =  input.trim()
                           .parse::<i32>()
                           .unwrap();
    
        if number % 2 == 0 { println!("YES"); }
        else { println!("NO"); }

        Ok(())
    }

