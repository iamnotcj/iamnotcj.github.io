+++
title = 'Way Too Long Words'
date = 2023-09-11T21:06:14-05:00
draft = true
+++
Say "Pneumonoultramicroscopicsilicovolcanoconiosis", please.
<!--more-->
## Introduction
### Problem Statement
![failed](/images/compprog/WayTooLongWords/ProblemStatement.png)

So basically if the string is longer than 10 we need to abbreviate it by 
sandwiching a number between the first and last characters of the word. The 
number that would be sandwiched is the amount of characters between the first 
and last characters of that same word.

![failed](/images/compprog/WayTooLongWords/input1.png)

In these examples:

Since "word" is less than 10 characters it doesn't get abreviated.
Since "localization" is more than 10 characters. The word gets abreviated to
"l10n":

``` 'l' == First Character ```

```10 == length of "o c a l i z a t i o"```

```'n' == Last Character```

### Concept
We can solve this problem by sandwiching  the length of the substring between the
first and last characters.

## Implementation

First we start with our input boilerplate. Since there are multiple words we 
have to abbreviate, we are going to create a vector that will hold our 
answers\.\.\.
    
    use std::io::stdin;

    fn main() -> Result<(), String> {
        let mut buffer = String::new();
        let mut output = Vec::new();

        let _ = stdin().read_line(&mut buffer);

        Ok(())
    }

The first thing we get from the input stream is the total amount of words we
need to abbreviate. We can use this as the upperbound of our while loop where 
we continue to grab words one at a time\.\.\.

    use std::io::stdin;

    fn main() -> Result<(), String> {
    //  <--snip-->
    //  #1
        let count = string.trim()
                          .parse::<u8>()
                          .unwrap();

    //  #2
        let mut x = 1; 
        while x <= count {
            string.clear();
            io::stdin().read_line(&mut string);

            let buffer = String::new();

        }

        Ok(())
    }

Here at #1 we use these functions on the string\.\.\.

"Trim()": This removes the newline character from the input.

"Parse()": This allows us to parse the string into an unsigned integer.

"Unwrap()": Allows us to use the item from the object returned from the previous
            function.

From there, at #2 we can create a while loop to grab the next bunch of words 
from the stream. 

We create another buffer within the loop to hold the new string
that we will output to the screen

Now for the logic, we can take the length of the original string
and if it is greater than 10, take the length of the substring and sandwich it
between the first and second characters. From there we can push it to our 
output vector.

If the length is less than 10, simply add the original string to our output
vector.

    use std::io::stdin;

    fn main() -> Result<(), String> {
    //  <--snip-->
        while x <= count {
            string.clear();
            io::stdin().read_line(&mut string);

            let buffer = String::new();
            let length = string.len() - 2;

    //      #1
            if length > 10 {
                output.push(buffer + &string[0..1] 
                                   + &(length-2).to_string() 
                                   + &string[length-1..length]);
            } else {
                output.push(string);
            }

            x += 1;

        }

        Ok(())
    }

Note for #1, a way to access a specific character for a String in Rust is to 
use slices, since to my understanding Rust doesn't allow you to directly 
index a string. The reason behind this is because all strings in Rust are 
UTF-8 encoded, so there isn't a guarantee that you will always access a valid
character if you are working with non ascii characters.

So rather than doing\.\.\.

``` 
    string = "Hello World"
    string[0] == 'H' 
```


We have to do something like this\.\.\.

```
    string = "Hello World"
    &string[0..1] == "H"
```

And from here we can just make a for loop to print out the results in our 
output vector.

    use std::io::stdin;

    fn main() -> Result<(), String> {
    //  <--snip-->
        for word in output {
            println!("{}", word);
        }

        Ok(())
    }

Alternatively, you can avoid using the output vector altogether by just 
printing the results while in the loop.

### Full Code Solution

    use std::io::stdin;

    fn main() -> Result<(), String> {
        let mut buffer = String::new();
        let mut output = Vec::new();

        let _ = stdin().read_line(&mut buffer);

        let count = string.trim()
                          .parse::<u8>()
                          .unwrap();

        let mut x = 1; 
        while x <= count {
            string.clear();
            io::stdin().read_line(&mut string);

            let buffer = String::new();
            let length = string.len() - 2;

            if length > 10 {
                output.push(buffer + &string[0..1] 
                                   + &(length-2).to_string() 
                                   + &string[length-1..length]);
            } else {
                output.push(string);
            }

            x += 1;

        }

        for word in output {
            println!("{}", word);
        }
        Ok(())
    }

