+++
title = 'Writing a Web Directory Bruteforce tool in Go'
date = 2023-11-09T21:47:21-06:00
draft = false
featured = true
+++
Simple and Multi-Threaded.
<!--more-->
## Introduction
The next project in the roadmap revolves around bruteforcing Web Directories.

For this project, I will be using Golang

### Design
My design consisted of 3 "portions"

User Interface: This will contain the prompts used for the CLI. Nothing too
crazy here.

Networking: The project requires me to utilize threads in the bruteforce. I 
came up with a solution where I would take the wordlist and divide it by the
amount of threads specified. 

Parser: For this portion of the program, we need to handle the file and parse
it into something we can work with

And now we can start writing the program.

## Implementation

Lets start with the main function:

    func main() {
        data, err := os.ReadFile("wordlist.txt")
        if err != nil {
            panic(err)
        }

        //parser()
        
        //bruteforce()

    }

First thing we are doing here is just reading a file. However, when we first
read the file we get back a stream of bytes. 

(Side note, I'm not sure if there is a function in the std library that would 
handle this next part I'm about to breakdown.)

So we need to parse the bytes into strings that we can then place into a 
wordlist for our program.

Here is the beginning of our parser function:
    
    func parser() {
        var buffer string
        wordlist := make([]string, 0)



        return wordlist
    }

Here we initialize 2 variables, a buffer to hold the strings we create from the
bytes and the wordlist we will return back to the program.

To create strings from our stream of bytes, we need to loop over the every byte
and add each byte to the buffer. When we reach a newline character, we take the 
buffer and add it to the wordlist. We then clear the buffer and rinse, wash, 
repeat.

Heres how it'll look:
    
    func parser() {
        var buffer string
        wordlist := make([]string, 0)
        
        // Our loop
        for x, i := 0, 0; x != len(b); x++ {
            // The 'newline' condition
            if b[x] == '\n' {
                wordlist = append(wordlist, buffer)
                buffer = ""
                i += 1
                continue
            }
            // Add bytes from the data stream to buffer
            buffer += string(b[x])
        }


        return wordlist
    }


Very nice. Lets move on

The next function that we need to make is the "bruteforce" function. We can lay 
it out here.

    func bruteforce() {
        length := len(wordlist)
        threads := 10
        


    }

Here we initialize the amount of threads we want to start and the length of 
our wordlist. This will make a little more sense when we start getting to the 
meat and potatos of the function.

    func bruteforce() {
        length := len(wordlist)
        threads := 10
        
        q := length / threads



    }

We need to split up the wordlist so that each thread (hopefully) gets an equal
amount of words to try.

There arrises an interesting problem of what happens when we don't get a number
of words that is evenly divisible by the amount of threads we have.

``` 26 words / 10 threads = 2 words a thread with 6 left over ```

We can account for this by taking the modulo of the length and the threads. 
We can then use the modulo as a guide for adding more words per thread up until
'x' thread is reached. 

This should make more sense after we write out the logic, which we will do so
now.

    func bruteforce() {
        length := len(wordlist)
        threads := 10
        
        // If you have less words than threads, just assign as many threads
        // as there are words.
        if length < threads {
            threads = length % threads
        }

        q := length / threads
        r := length % threads

        // #1 Loop Condition
        for x, lower := 0, 0; x < threads; x += 1 {
            var slice []string
            // #2 Modulo conditions
            if r != 0 && x <= r {
                slice = wordlist[lower: lower + q + 1]
                lower += q + 1
            } else {
                slice = wordlist[lower: lower + q]
                lower += q
            }

            // #3 Start threads
            go connect(url, slice)

        }
    }

If we take a look at #1, we initialize a variable 'x', which will keep track of 
the number of threads being spawned, and 'lower', which will keep track of the
lower bound of the wordlist that we are slicing.

Moving to #2, we are creating a slice of our wordlist. We establish a condition
that will allow us to add more words to the threads if the division at the 
top of the function yields any remainders.

Here is an example to illustrate this. Say we have 4 words and 2 Threads:
    
    Words = [bruh, breh, brah, bro]
    Threads = 2

    // Since 4 / 2 = 2, each thread gets 2 words

    Loop twice for 2 threads:
        Iteration #1:
            Thread #1 = [bruh, breh]

        Iteration #2:
            Thread #2 = [brah, bro]
        
But what if we have five words? 

    Words = [bruh, breh, brah, bro, bra]
    Threads = 2

    // Since 5 / 2 = 2, each thread gets 2 words

    Loop twice for 2 threads:
        Iteration #1:
            Thread #1 = [bruh, breh]

        Iteration #2:
            Thread #2 = [brah, bro]

At the end, we are missing 'bra'. In integer division 5 / 2 = 2 with a 
remainder of 1. We can fix this by adding 3 on the first iteration and 2 on
the next iteration.

    Words = [bruh, breh, brah, bro, bra]
    Threads = 2

    // Since 5 / 2 = 2, each thread gets 2 words
    // Since 5 % 2 = 1, there is 1 word left over

    Loop twice for 2 threads:
        if current iteration is less than the remainder :
            Add 1 extra word to the thread being spawned.
            
        Iteration #1:
            Thread #1 = [bruh, breh, brah]

        Iteration #2:
            Thread #2 = [bro, bra]

And now with this condition, we can specify a fixed amount of threads and also
make sure that all words are being utilized.

And with that we have created our bruteforce function, now we need to handle
actually connecting to the website and checking if the directory is actually
valid.

We can do so by simply connecting to the site with the words from the slice we
just made and if the server returns a 200, we know that this is a valid
directory.

    func connect() {
        for x := 0; x < len(s); x++ {
            res, err := http.Get(url + s[x])
    
            if res.StatusCode == 200 && err == nil && s[x] != "" {
                fmt.Println("200 Response: ", (url + s[x]))
            }
        }
    
    }

Here we initialize a loop that iterates over the slice. Upon each
iteration, we connect to the server and use the response to see if the 
directory actually exists or not.

Note: On the last conditional, there seems to be a 'blank' word that always 
sneaks itself into the wordlist. The ' s[x] != "" ' ignores the response where
the url is the same as the one that the user provided.

Is there a better way to handle this?

Most definitely!

Will I fix it?

![failed](/images/Miscellaneous/100/dir_brute/nope.gif)

Now there are two things left we need to do. We need to go back and add
parameters to the functions, as well as initializing said variables in the main
function.

We also need to set up a wait group for our threads, to ensure that we don't
complete the program before our threads finish.

Lets start wit the connect function since we are already there.

    func connect(url string, s []string, wg *sync.WaitGroup)
        // <--snip-->

        wg.Done()
    }

We put wg.Done() at the end to signify that the thread is complete.

Moving back to the bruteforce function from before

    func bruteforce(url string, wordlist []string, threads int)
        // <--snip-->

        var wg sync.WaitGroup
        wg.Add(threads)

        // <--snip-->


        wg.Wait()
    }

We initialize the wait group and add the amount of threads we expect to use to
it. We then call wg.Wait() to ensure that the function will wait on the 
completion of the threads we specified earlier before continuing. We will
also let the main function handle the "threads" variable.

And now we move back to the main function. Here we can create all of the 
variables that we will be passing to the functions.

    func main() {
        dat, err := os.ReadFile("wordlist.txt")
        if err != nil {
            panic(err)
        }
    
        var url string
        wordlist := parser(dat)
        threads := 10
    
        fmt.Println("Please enter the URL")
        fmt.Scanln(&url)
    
        bruteforce(url, wordlist, threads)
    }

And that is pretty much the program! The code in full is presented below.

## Full Code Implementation

    package main

    import (
        "fmt"
        "os"
        "net/http"
        "sync"
    )
    
    func connect(url string, s []string, wg *sync.WaitGroup) {
        for x := 0; x < len(s); x++ {
            res, err := http.Get(url + s[x])
    
            if res.StatusCode == 200 && err == nil && s[x] != "" {
                fmt.Println("200 Response: ", (url + s[x]))
            }
        }
    
        wg.Done()
    }

    func bruteforce(url string, wordlist []string, threads int) {
        length := len(wordlist)

        if length < threads {
            threads = length % threads
        }
        
        q := length / threads
        r := length % threads

        var wg sync.WaitGroup
        wg.Add(threads)

        for x, lower := 0, 0; x < threads; x += 1 {
            var slice []string
            if r != 0 && x <= r  {
                slice = wordlist[lower: lower + q + 1]
                lower += q + 1
            } else {
                slice = wordlist[lower: lower + q]
                lower += q
            }

            go connect(url, slice, &wg)
        }

        wg.Wait()
    }
    
    func parser(b []byte) []string {
        var buffer string
        wordlist := make([]string, 0)
        
        for x, i := 0, 0; x != len(b); x++ {
            if b[x] == '\n' {
                wordlist = append(wordlist, buffer)
                buffer = ""
                i += 1
                continue
            }

            buffer += string(b[x])
        }

        return wordlist
    }

    func main() {
        // You can change this to whatever file you want. I'm too lazy to add
        // a prompt for this.
        dat, err := os.ReadFile("wordlist.txt")
        if err != nil {
            panic(err)
        }
    
        var url string
        wordlist := parser(dat)
        threads := 10
    
        fmt.Println("Please enter the URL")
        fmt.Scanln(&url)
    
        bruteforce(url, wordlist, threads)
    }

Not bad! Now if we run the program with a wordlist and target google.com

``` 
     Please enter the URL
     https://www.google.com/ 

```

We get\.\.\.

```
200 Response: https://www.google.com/health
200 Response: https://www.google.com/home
200 Response: https://www.google.com/history
```
