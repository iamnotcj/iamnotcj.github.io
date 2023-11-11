+++
title = 'Even Odds'
date = 2023-10-03T01:39:39-05:00
draft = true
+++
Evening the odds?
<!--more-->
## Introduction
### Problem Statement
![failed](/images/compprog/evenodds/ProblemStatement.png)

So basically, we are finding what number will be at a certain position in a set
of numbers. The set of numbers consists of (in order) all of the odd numbers up
to a specified number, followed by all of the even numbers up to the specified
number.
![failed](/images/compprog/evenodds/input1.png)
In this example if we lay out the ordered set we get something like:

``` {1, 3, 5, 7, 9, 2, 4, 6, 8, 10}```

The third place in this set is the number 5, so 5 is the answer.

### Concept

We can solve this mathematically, we just need to keep in mind of a couple of 
properties.

There are either 2 sets: 
* One where half the numbers are odd or even.
* One where there are more odds than evens.

We can find the point in the set where we reach the end of the odd numbers. 
From there, if the position we are looking for is below this point, we can 
compute the appropriate odd number that should be at that position. 

It's a similar process for when the position is above the point. We would then 
compute the appropriate even number.
## Implementation

We can start off with handling the input

    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
    
        let _ = stdin().read_line(&mut buffer);
    
    
        Ok(())
    }

We then have to parse the first line of input, this line gives us the length
of the set and the position to look up when we are done.
        
    let input: Vec<u64> = buffer.trim()
                                .split(" ")
                                .map(|x| x.parse::<u64>().unwrap())
                                .collect();
    
Here we are chaining methods together.

Trim(): Strip off the newline character from the buffer

Split(): Creates an iterator from splitting the string by whitespace.

Map(): We can consume the previous iterator and create another iterator whose 
elements are the numbers parsed from the input.

Collect(): We can then consume the iterator and create a Vector out of it.

We can now start implementing the logic of our program. First, we should find 
the point in the set where the "odds" stop and the "evens" begin.

        let mut half = 0;
    
        if input[0] % 2 != 0 {
            half = (input[0] / 2) + 1; 
        } else {
            half = input[0] / 2;
        }

Here we create a conditional where if the length of the set is odd, we perform
an integer division by 2 and add 1. This will give us the point where the odds
stop.

An example:
``` 
{1, 3, 5, 2, 4} is 5 elements long.

5 / 2 = Position 2 (3).

Position 2 + 1 = Last odd number (5)
```

If the length of the set is even, the point where the odds stop will be at 
exactly half of the length of the set.

An example:
``` 
{1, 3, 2, 4} is 4 elements long.

4 / 2 = Position 2 (3).

Position 2 is the last odd number.
```

Now we can move on to actually calculating the number at the position.

    let mut current: u64 = 0;

    if input[1] <= half {
        current = 1;
        current += (input[1] - 1) * 2;
    } else {
        current = 0;
        current += (input[1] - half) * 2;
    }

In the first conditional, we check to see if the position is less than or equal
to half (which is the point where the odd numbers stop). If this is true, since
the first odd number is 1, we update the 'current' variable accordingly. We then
take the position and subtract it by 1 before multiplying by 2. We add the result
of that to our current variable

If the position is greater than half, we can subtract the position by half and
multipy by 2 to get the correct number.

And after that, all we have to do is print the current variable and we can 
complete the challenge. Here is the full solution.

## Full Code Solution
    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
    
        let _ = stdin().read_line(&mut buffer);
    
        let input: Vec<u64> = buffer.trim()
                                    .split(" ")
                                    .map(|x| x.parse::<u64>().unwrap())
                                    .collect();
    
        let mut half = 0;
    
        if input[0] % 2 != 0 {
            half = (input[0] / 2) + 1; 
        } else {
            half = input[0] / 2;
        }
    
        let mut current: u64 = 0;

        if input[1] <= half {
            current = 1;
            current += (input[1] - 1) * 2;
        } else {
            current = 0;
            current += (input[1] - half) * 2;
        }
    
        println!("{current}");
    
    
        Ok(())
    }
