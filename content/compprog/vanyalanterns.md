+++
title = 'Vanya and Lanterns'
date = 2023-10-06T20:30:50-05:00
draft = false
featured = true
+++
It's 1AM and I don't have something to write here.
<!--more-->
## Introduction
### Problem Statement

![failed](/images/compprog/vanyaandlanterns/ProblemStatement.png)

So we are setting up street lights, and we need to find the minimum light radius
that will be enough to light whole street. We are given points on the street
where we are required to put a light up.

![failed](/images/compprog/vanyaandlanterns/input1.png)

In this example, the minimum light radius that each street light needs to light
up the entire street is 2.5.
### Concept

We need to find the greatest distance between two street lamps. We can first 
sort the array, and then find the difference between the lamp we are currently
looking at and the previous lamp.

From there we need to divide the difference by 2, since the distance is the
culmination of the radiuses (radii?) of the current lamp and the previous.

We also need to check and see if the distance between the first lamp and the
beginning of the street is greater than the radius we find in our algorithm.

We also got to do the same for the last street lamp and the end of the street.

## Implementation
We need to first handle the input. It will look something like this.

    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
    
        let _ = stdin().read_line(&mut buffer);
    
    }

Next we need to parse the first line which will give us the number of street
lamps and the length of the street.

    let options = buffer.trim()
                        .split(" ")
                        .map(|x| x.parse::<f64>().unwrap())
                        .collect::<Vec<f64>>();

Here we are chaining together 4 methods:

Trim(): This allows us to shave off the newline character in the buffer.

Split(): This allows us to create an iterator, whose elements are the numbers of
the buffer.

Map(): This allows us to consume the previous iterator and create a new iterator 
where those numbers are parsed as floats.

Collect(): This allows us to consume the previous iterator and create a Vector
of floats.
    
Next we need to handle the second line, which will give us the positions of the
lamps on the street.
    
    buffer.clear();
    let _ = stdin().read_line(&mut buffer);
    
    let mut input = buffer.trim()
                          .split(" ")
                          .map(|x| x.parse::<f64>().unwrap())
                          .collect::<Vec<f64>>();
    
Here we do the same thing that we did for the first line of input. The only
difference is we have to clear the buffer before handling more input.

Next we need to sort the array with all of the lamps. We can do so by doing this

    input.sort_by(|a, b| a.partial_cmp(&b).unwrap());

This closure allows us to sort the array in ascending order.

We can now work on the logic of the program. We want to start at the 2nd element
and take the difference between that element and the first element. We will
record it is as the maximum amount of distance. Upon each iteration,
we will check the distance with another distance that we calculate and update
accordingly.

It will look something like this.

    let length = options[0];
    let mut max_distance: f64 = 0.0;
    for x in 1..length {
        let curr_distance = input[x] - input[x-1];
        if curr_distance > max_distance {
            max_distance = curr_distance;
        }
    }

When we break out of the loop we will have found the largest distance between
any given lights. We then divide by 2 to get the radius. However, we still need 
to account for the first and last lights.

It is possible that the first light occurs far away from the beginning of the
street.

It is also possible that the last light occurs far away from the end of the 
street.

We can create a conditional to check for these occurences.

    let max_radius = max_distance / 2.0;
    
    if input[0] - 0.0 > max_radius {
        max_radius = input[0] - 0.0;
    }
    if options[1] - input[length-1] > max_radius {
        max_radius = options[1] - input[length-1];
    }

In the first conditional, we are checking to see if the distance between
the first street lamp and the beginning of the street is greater than the
maximum we already have. (note: I know the 0.0 is redundant.)

If it is, we update our max radius to account for that.

In the last conditional, we are checking to see if the distance between the 
last street lamp and the end of the street is greater than the maximum that we
have, and if it is update accordingly.

And that is the program! All we have to do is print the maximum and we have 
solved this challenge. Here is the full code solution.


### Full Code Solution
    use std::io::stdin;
    
    fn main() -> Result<(), String> {
        let mut buffer = String::new();
    
        let _ = stdin().read_line(&mut buffer);
    
        let options = buffer.trim()
                            .split(" ")
                            .map(|x| x.parse::<f64>().unwrap())
                            .collect::<Vec<f64>>();
    
        buffer.clear();
        let _ = stdin().read_line(&mut buffer);
    
        let mut input = buffer.trim()
                              .split(" ")
                              .map(|x| x.parse::<f64>().unwrap())
                              .collect::<Vec<f64>>();
    
    
        input.sort_by(|a, b| a.partial_cmp(&b).unwrap());
    
        let length = options[0];
        let mut max_distance: f64 = 0.0;
        for x in 1..length {
            let curr_distance = input[x] - input[x-1];
            if curr_distance > max_distance {
                max_distance = curr_distance;
            }
        }
        
        let max_radius = max_distance / 2.0;
    
        if input[0] - 0.0 > max_radius {
            max_radius = input[0] - 0.0;
        }
        if options[1] - input[length-1] > max_radius {
            max_radius = options[1] - input[length-1];
        }
        println!("{max_radius}");
    
        Ok(())
    
    }
