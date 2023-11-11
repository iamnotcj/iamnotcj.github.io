+++
title = 'Burglar And Matches'
date = 2023-10-01T23:39:11-05:00
draft = true
+++
Get in loser, we're robbing matches!
<!--more-->
## Introduction
## Problem Statement
![failed](/images/compprog/burglarandmatches/ProblemStatement.png)

So we have a burglar who is stealing matches from a warehouse. There are 
containers that carry 'x' matchboxes. He can only carry a certain amount of 
matchboxes. Each matchbox contain 'y' matches. Our goal is to figure
out the most amount of matches we can steal from the warehouse. 
![failed](/images/compprog/burglarandmatches/input1.png)

In this example, the most amount of matches we can take is 62. The most amount
of boxes we can take is 7, so we can take 5 of the boxes with 10 matches and 
2 of the boxes with 6 matches. The total amount of matches that we just stole
would equal 62.

### Concept

We can develop an algorithm where we always start at the container with the 
most matches. From there, we can just take as many boxes as we can in a 
descending order. 

## Implementation

First we start off with our boilerplate.

    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
    
        let _ = stdin().read_line(&mut buffer);
    
        Ok(())
    }
    
Here we initalize some variables to handle the input.

Moving on to when we actually receive the first line of input. We can parse it 
like this.

    let mut options = buffer.trim()
                            .split(" ")
                            .map(|x| x.parse::<i32>().unwrap())
                            .collect::<Vec<i32>>();

Here we are chaining methods together to return a 2 element vector of the first
row (named options).

Trim(): Allows us to shed off the newline character from the input string.

Split(): Allows us to create an iterator, whose elements are the characters 
split by whitespace.

Map(): Allows us to consume this iterator and create a new iterator, where we
parse the elements into an integer.

Collect(): This is where we can collect the elements of the iterator and create
a Vector from them.

Now we can move on to handling the containers input.

    let mut containers: Vec<Vec<i32>> = Vec::new();

    for _x in 0..options[1] {
        buffer.clear();
        let _ = stdin().read_line(&mut buffer);
        let container = buffer.trim()
                              .split(" ")
                              .map(|x| x.parse::<i32>().unwrap())
                              .collect::<Vec<i32>>();

        containers.push(container);
    }

Here we create a Vector whose elements are Vectors containing integers. We 
then create a loop that runs for options[1], which denotes how many lines of 
input we should expect.

The meat of the loop is similar to the last batch of code we looked at. We take
the input and parse it all the way to a Vector of integers (container). We then
take the container and push it into another vector called "containers"

With all of the input handled, we can now move on to the logic of our program.
We first need to sort the newly created containers vector. We can do that with 
something like this.

(Snipped for clarity)

    containers.sort_by(|a, b| b[1].cmp(&a[1]));

The closure allows us to sort the containers vector by descending order. With 
that out of the way, lets move on to the final part of the program.

    let mut total = 0;
    for container in containers {
        if options[0] == 0 {
            break;
        }

        if options[0] >= container[0] {
            total += container[0] * container[1];
            options[0] -= container[0];
        } else {
            total += options[0] * container[1];
            options[0] = 0;
        }
    }

Here we initialize a variable to hold the total amount of matches we can carry.
We then create a for loop that iterates over each container. We have a 
conditional where if the amount of matchboxes we can carry is 0, we simply break
out of the loop.

The next conditional is where we add to the total. If we can carry more 
matchboxes then what is currently in the container, drain the container.

If the container has more than we can carry, take the most amount you can from
the container.

After this, all we need to do is add a print function and we are done with this
challenge! 

### Full Code Solution
    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
    
        let _ = stdin().read_line(&mut buffer);
    
        let mut options = buffer.trim()
                                .split(" ")
                                .map(|x| x.parse::<i32>().unwrap())
                                .collect::<Vec<i32>>();
    
        let mut containers: Vec<Vec<i32>> = Vec::new();
    
        for _x in 0..options[1] {
            buffer.clear();
            let _ = stdin().read_line(&mut buffer);
            let container = buffer.trim()
                                  .split(" ")
                                  .map(|x| x.parse::<i32>().unwrap())
                                  .collect::<Vec<i32>>();
    
            containers.push(container);
        }
    
        containers.sort_by(|a, b| b[1].cmp(&a[1]));
    
        let mut total = 0;
        for container in containers {
            if options[0] == 0 {
                break;
            }
    
            if options[0] >= container[0] {
                total += container[0] * container[1];
                options[0] -= container[0];
            } else {
                total += options[0] * container[1];
                options[0] = 0;
            }
        }

        println!("{total}");
        Ok(())
    }



