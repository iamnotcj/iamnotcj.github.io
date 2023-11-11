+++
title = 'Writing a simple port scanner in Rust'
date = 2023-11-07T21:47:09-06:00
draft = true
featured = true
+++
A port scanner that will be expanded upon in future blog updates. 
<!--more-->
## Introduction
The first project in this roadmap is a port scanner.

I will be writing it in Rust.

### Design
For this program I have 3 portions:

UI: This will be where the prompt and input are handled. Nothing to crazy in 
this portion of the program.

Networking: This will be where we send tcp packets to specified ports and see
if a connection is successful.

Parser: This will be where we handle data from the input and mold it into 
something usable for the program

And now we can start writing the program.

## Implementation

We will start with our main function
    

    // main.rs

    fn main() {
        run();
    }

Here we have a run function this will be where the meat and potatos of our
program will be.

We can map out the run function like this

    // main.rs

    fn run() {
        ui::intro_display();

        ui::prompt_addr();
        ui::prompt_ports();

        scanner::start();
    }

Here we can see some of the functions that belong to the portions we mapped out
at the beginning. This is only a prototype of how things will actually look.
For now, we can start building out the UI module.

In the UI module, we can start with the 'intro\_display()' function

    // ui.rs
    
    pub fn intro_display() {
        println!("A Limited Port Scanner in Rust");
        println!("Good Luck and Have Fun!\n\n\n");
    }

Now that our prompt that greets the user is done, lets move on to the prompt
where we ask the user for an address.

    // ui.rs

    pub fn prompt_ip_addr() -> String {
        let mut input = String::new();
    
        println!("Please enter the IP Address: ");
        let _ = stdin().read_line(&mut input);
        
    }

First we initialize the input buffer that will hold the string that the user
enters. We, of course, give the user a prompt for the address like so.

After getting the string, we need to validate the string. We can do this by 
parsing the string into 8 bit numbers and checking to see if the string follows
the rules that make up a Ipv4 address.

    // ui.rs

    pub fn prompt_ip_addr() -> Ipv4Addr {
        println!("Please enter the IP Address: ");
    
        let mut input = String::new();
        let _ = stdin().read_line(&mut input);

        parser::ip_addr(input);       

    }

Lets finish out this module first before implementing all of the parsing logic.
Next we have to parse the ports that the user specifies.

I thought it would be nice to allow the user to specify if they wanted to scan
for ALL ports or just the "Most common" ports. We will implement something 
like that later.

This function is pretty similar to the one we just did. It looks something like
this.
    
    // ui.rs

    pub fn prompt_ports() -> Vec<u16> {
        println!("Please enter the Ports");
        println!("(Leave empty to scan most common)"); 
        println!("(Enter \"ALL\" to scan all)");

        let mut input = String::new();
        let _ = stdin().read_line(&mut input);
    
        parser::ports(input);
    }

Now we can move on to the parser module! We will start off with our 
IP addressing.

Note from Editor CJ: I was going to first talk about writing the test cases for 
the parser module. However, I figured that it would be kinda long (and boring). 
It's exactly what you'd think a test case for this would be.

    pub fn ip_addr(input: String) -> Result<(), String> {
        let mut errors = Vec::new();
        let components: Vec<u8> = input

        Ok()
    }

Ok, so we initialize 2 Vectors. One will hold all of the errors of the parsing
operations we are about to do (it might make more sense when we get there) and 
we have a components vector that will hold the 4 8-bit numbers that make up an
IPv4 Address.

So onto the actual parsing of the input. We can chain various string and 
iterator methods together to reach the target output that we want. It will 
look something like this

(Snipped and simplified for clarity)

    pub fn ip_addr() {
        components = input.trim()
                          .split(".")
                          .map(|x| x.parse::<u8>())
                          .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
                          .collect();
    }

Lets go through each method in the chain:

Trim: This will allow us to strip the newline character from the input.

Split: This allows us to create an iterator by splitting the string by each 
period and iterating over them.

Map: This allows us to create another iterator where the elements are the 
results of parsing each element of the previous iterator to 8-bit numbers.

Filter\_Map: This allows us to filter out any errors we got in our iterator from
the previous method. The errors populate the error vector. We need to do this 
because, if there is an error (like someone inputs 127.a.0.1 for instance) we 
would like to handle it by alerting the user and exiting the program. This 
method returns a new iterator consisting of only the numbers that were parsed 
correctly.

Collect: This allows us to turn the iterator into a vector.

Now that we have properly parsed our input, lets handle the errors in our error
vector.
    
    // parser.rs

    pub fn ip_addr(input: String) -> Result<(), String> {
        let mut errors = Vec::new();
        let components:  Vec<u8> = input.trim()
                                       .split(".")
                                       .map(|x| x.parse::<u8>())
                                       .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
                                       .collect();
    
        // #1 If there is a single error in the vector
        if errors.len() != 0 {
            return Err(format!("Failed to parse IP Address: Invalid character"))
        }
            
        // #2 If there isn't exactly 4 components
        if components.len() != 4 {
            return Err(format!("Failed to parse IP Address: Invalid length"))
        }
        
        // #3 The string is a valid ipv4 address
        Ok(())
    }

If we take a look at #1, an empty error vector means that all the components of
the string were parsed correctly. 

Moving on to #2, if the length of the components vector doesn't equal exactly
4, that means that the IP address length is invalid (i.e. 127.0.0 or 8.8.8.8.8)

At #3, if we reach this statement, that means that the string that was supplied
is a vaild Ipv4 Address. We don't have to worry about the correctness of it in
future usage of this program.




And that is the function! Lets move on to the ports.

    // parser.rs
    
    pub fn ports(input: String) -> Result<Vec<u16>, String> {
        let mut ports: Vec<u16> = Vec::new();
    
        match input.as_str() {

        }
    }

Here we created a Vector which will hold all of our ports to scan, and we will
be using a match statement on the input. 

Now our match statement will have three cases. Lets first start with the "ALL"
case.

(Snipped and simplified for clarity)

        "ALL\n"  => {    for x in 0..=u16::MAX {
                           ports.push(x);
                         }
                         return Ok(ports);
                    }

Here when the user types in the word "ALL" we push every valid port number into
our ports vector and return that vector to our program.

Moving on to our next case

(Snipped and simplified for clarity)

        "\n"     => {    ports.push(22);
                         return Ok(ports);
                    }

Here if the user just presses enter, we push the most common ports to the 
ports vector. For now, we will just push port #22.

And now for when the user supplies us with numbers.

(Snipped and simplified for clarity)

         _       => {   let mut errors = Vec::new(); 
                        ports = input.trim()
                                     .split(" ")
                                     .map(|x| x.parse::<u16>())
                                     .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
                                     .collect();


                        if errors.len() != 0 {
                            return Err(format!("Failed to parse ports"))
                        }
                            
                        return Ok(ports);
                        }
                    }

This follows the same logic of the IP address parser. There are some slight
differences though, like instead of splitting on '.' we split on spaces (' ')
and etc.

Now we can just combine these cases into the match statement and we are done 
with this module!

    // parser.rs

    pub fn ip_addr(input: &mut String) -> Result<(), String> {
        let mut errors = Vec::new();
        let components:  Vec<u8> = input.trim()
                                       .split(".")
                                       .map(|x| x.parse::<u8>())
                                       .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
                                       .collect();
    
    
        if errors.len() != 0 {
            return Err(format!("Failed to parse IP Address: Invalid character"))
        }
            
    
        if components.len() != 4 {
            return Err(format!("Failed to parse IP Address: Invalid length"))
        }
    
    
        Ok(())
    }
    
    
    pub fn ports(input: String) -> Result<Vec<u16>, String> {
        let mut ports: Vec<u16> = Vec::new();
    
        match input.as_str() {
            "ALL\n"  => {    for x in 0..=u16::MAX {
                               ports.push(x);
                             }
                             return Ok(ports);
                        }
                    
            "\n"     => {    ports.push(22);
                             return Ok(ports);
                        }
    
             _       => {   let mut errors = Vec::new(); 
                            ports = input.trim()
                                         .split(" ")
                                         .map(|x| x.parse::<u16>())
                                         .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
                                         .collect();
    
    
                            if errors.len() != 0 {
                                return Err(format!("Failed to parse ports"))
                            }
    
                            return Ok(ports);
                        }
    
        }
    
    }

Ok, now we can go back to the UI module and handle any errors returned by the
parser functions. We can create a match statement that follows this general
structure
    
    if let Ok(x) = parser::BRUH {
        return x
    } else {
        println!("{e}");
        process::exit(0x01);
    }
        
So our updated UI module will look like this.

    // ui.rs

    use std::io::stdin;
    use std::process;
    use crate::parser;
    
    pub fn intro_display() {
        println!("A Limited Port Scanner in Rust");
        println!("Enjoy and have fun!\n\n");
    }
    
    pub fn prompt_ip_addr() -> String {
        println!("Please enter the IP Address: ");

        let mut input = String::new();
        let _ = stdin().read_line(&mut input);
        
        if let Ok(x) = parser::ip_addr(&mut input) {
            return input
        } else {
            println!("{e}");
            process::exit(0x01);
        }
    }
    
    pub fn prompt_ports() -> Vec<u16> {
        println!("Please enter the Ports");
        println!("(Leave empty to scan most common)"); 
        println!("(Enter \"ALL\" to scan all)");

        let mut input = String::new();
        let _ = stdin().read_line(&mut input);
    
        if let Ok(x) = parser::ports(input) {
            return x
        } else {
            println!("{e}");
            process::exit(0x01);
        }
    }

Ok, now we have one more module to take care of. Recall that back in our main module
we had a function called scanner::start().

    // main.rs

    fn run() -> Result<(), Error> {
        ui::intro_display();

        ui::prompt_addr();
        ui::prompt_ports();

        // This function
        scanner::start()?;

        Ok(())
        
    }

Lets get started! It will look something like this.

    // scanner.rs
    
    pub fn start(ip_addr: Ipv4Addr, ports: Vec<u16>) -> Result<(), Error>{
            for x in ports {
                let address = format!("{ip_addr}:{x}");

                if let Ok(y) = TcpStream::connect(&address) {
                    println!("Port {x} is open");
                } else {
                    println!("Port {x} is closed");
                }
            }
    
            Ok(())
    }

Here we iterate over the ports in the ports vector. We then create a string
of the full address, combining the ip address and the port.

From there we actually connect to the address with the connect() address and 
handle the response accrodingly.

And with that we have completed our program! Here is the program in full.

## Full Code Implementation.

Here is main.rs:

    // main.rs

    mod ui;
    mod scanner;
    mod parser;
    
    fn run() {
        ui::intro_display();
    
        let ip_addr = ui::prompt_ip_addr();
        let ports = ui::prompt_ports();
    
        scanner::start(ip_addr, ports);
    
        Ok(())
    }
    
    fn main() {
        run();
    }

Here is our ui.rs module:

    // ui.rs

    use std::io::stdin;
    use std::process;
    use crate::parser;
    
    pub fn intro_display() {
        println!("A Limited Port Scanner in Rust");
        println!("Enjoy and have fun!\n\n");
    }
    
    
    pub fn prompt_ip_addr() -> String {
        println!("Please enter the IP Address: ");

        let mut input = String::new();
        let _ = stdin().read_line(&mut input);
        
        if let Ok(x) = parser::ip_addr(&mut input) {
            return input;
        } else {
            println!("Error in parsing IP address");
            process::exit(0x01);
        }
    }
    
    
    pub fn prompt_ports() -> Vec<u16> {
        println!("Please enter the Ports");
        println!("(Leave empty to scan most common)"); 
        println!("(Enter \"ALL\" to scan all)");

        let mut input = String::new();
        let _ = stdin().read_line(&mut input);
    
        if let Ok(x) = parser::ip_addr(&mut input) {
            return x;
        } else {
            println!("Error in parsing Ports");
            process::exit(0x01);
        }
    }

Here is our scanner.rs module:
    
    // scanner.rs
    
    use std::net::TcpStream;
    
    pub fn start(ip_addr: String, ports: Vec<u16>) {
            for x in ports {
                let mut address = format!("{ip_addr}:{x}");
                if let Ok(mut stream) = TcpStream::connect(&address) {
                    println!("Port {x} is open");
                } else {
                    println!("Port {x} is closed");
                }
            }
    
    }

And last but not least, here is our parser.rs module:

    // parser.rs

    pub fn ip_addr(input: &mut String) -> Result<(), String> {
        let mut errors = Vec::new();
        let components:  Vec<_> = input.trim()
                                       .split(".")
                                       .map(|x| x.parse::<u8>())
                                       .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
                                       .collect();
    
    
        if errors.len() != 0 {
            return Err(format!("Failed to parse IP Address: Invalid character"))
        }
            
    
        if components.len() != 4 {
            return Err(format!("Failed to parse IP Address: Invalid length"))
        }
    
        Ok(())
    }
    
    
    pub fn ports(input: String) -> Result<Vec<u16>, String> {
        let mut ports: Vec<u16> = Vec::new();
    
        match input.as_str() {
            "ALL\n"  => {    for x in 0..=u16::MAX {
                               ports.push(x);
                             }
                             return Ok(ports);
                        }
                    
            "\n"     => {    ports.push(22);
                             return Ok(ports);
                        }
    
             _       => {   let mut errors = Vec::new(); 
                            ports = input.trim()
                                         .split(" ")
                                         .map(|x| x.parse::<u16>())
                                         .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
                                         .collect();
    
    
                            if errors.len() != 0 {
                                return Err(format!("Failed to parse ports"))
                            }
    
                            return Ok(ports);
                        }
        }
    
    
    }

Now you might be asking:

"Wasn't there something in the parsing module we were going to do later?"

Yes, you are right! We needed to replace what we had for a case in ports, where
if the user wants to scan the most common ports, they would just hit enter. We
can do this by creating a Vector of the most common ports and just iterating
over them and pushing them to the ports vector. Similar to what we did in the
case above it.

"So, are you going to do it?"

![failed](/images/Miscellaneous/100/base_simp/No.jpg)

