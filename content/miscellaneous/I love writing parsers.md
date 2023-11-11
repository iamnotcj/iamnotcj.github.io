+++
title = 'I_L0V3_WR!T1NG_P@RSERS'
date = 2023-09-08T15:25:19-05:00
draft = true
featured = true
+++
A detour in one of my projects, where I write a Bencode Parser and learn some
neat stuff along the way.
<!--more-->
## Introduction
I'm currently writing a BitTorrent client, and a portion of my program has to
handle files encoded in "bencode". I've never heard of this encoding scheme
before, so I thought it would be nice to implement a decoder for this scheme.

I've never actually made one before, so after a fair amount of research on 
parsers here are the results!


### Design 
There are a couple of rules in the bencode scheme. I will list them here:
> An integer is encoded as 'i'\<integer\>'e' (ex: -42 = i-42e)
>
> A "string" is denoted by \<length\>:\<contents\> (ex: spam = 4:spam)
>
> A list of values is encoded as l\<contents\>e (ex: [spam, 42] = l4:spami42ee)
>
> A dictionary is encoded as d\<contents\>e. This is kind of the same as the list.
> However, the key has to be a string. (ex: {"bar": "spam", "foo": 42} =
d3:bar4:spam3:fooi42ee

Our approach here will be to just map out the primitive rules. Any of the more
"abstract" combinations like Dictionary within a Dictionary, should follow if 
we handle these primitive rules first.


## Implementation

I wanted to create a structure that would best represent these four rules, so
I made an enum.

    #[derive(PartialEq,Eq, Debug)]
    pub enum Token {
        Int(String),
        Str(String),
        Raw(Vec<u8>),
        List(Vec<Token>),
        Dict(HashMap<String, Token> )
    }

This makes alot of sense when we write the "parse" function.

    pub fn parse<I>(iter: &mut Peekable<I>) -> Result<Token, String> 
        where 
            I: Iterator<Item = u8>
    {
        match iter.peek().unwrap() {
            b'i'        => parse_integer(iter),
            b'l'        => parse_list(iter),
            b'd'        => parse_dict(iter),
            b'0'..=b'9' => parse_string(iter),
            _           => todo!()
        }
    
    }

Let's start at the top of the function. This function takes a 'Peekable'
iterator as an argument and returns one of our Tokens or an Error string.

Now if we move down to our match statement, we have different cases that will
lead us down to different functions. The cases are denoted by the first 
character of their ruleset.

Let's take a look at the parse\_integer() function.  

    fn parse_integer<I>(iter: &mut Peekable<I>) -> Result<Token, String> 
        where 
            I: Iterator<Item = u8>
    {
        let mut number = String::new();
        // #1
        iter.next();
        while let Some(x) = iter.peek() {
            // #2
            if *x == b'e' {
                iter.next(); 
                return Ok(Token::Int(number)); 
            } else {
                number.push(iter.next().unwrap() as char);
            }
        }
        Err(format!("Integer Error"))
    }

The top of the function is the same as the previous. In fact, all of the 
functions in this module will share the same introduction.

At #1, since we know that the next character in the iterator is an 'i', we can
go ahead and consume that character so we can start with the logic of our
function.

At #2, we take a peek at the next character at the start of each iteration of 
the loop. From there we judge if the character is a terminating character ('e')
or not. If it is, we will consume the character and return the number as a 
Token.

If the character is not the terminating character, we simply push the character
to the buffer that we created at the start of the function.

Lets take a look at the parse\_string() function.

    fn parse_string<I>(iter: &mut Peekable<I>) -> Result<Token, String> 
        where 
            I: Iterator<Item = u8>
    {
        // #1
        let mut buffer = String::new(); 
        let mut number = 0;
    
    
        // #2
        while let Some(x) = iter.next() {
            match x {
                b':'        => {  
                    number = buffer.parse::<i32>()
                                   .unwrap(); 
                                                      
                    break;      
                },
                b'0'..=b'9' => buffer.push(x as char),
                _           => return Err(format!("Invalid Size"))
            }
        }
    
        // #3
        let mut buffer = Vec::new();
    
        for x in 0..number {
           buffer.push(iter.next().unwrap()); 
        }
    
    
        // #4
        if let Ok(x) = String::from_utf8(buffer.clone()) {
            return Ok(Token::Str(x));
        } else {
            return Ok(Token::Raw(buffer));
    
        }
            
    }

Recall that strings will start off with its length, followed by a colon, 
followed by the string itself. 

With this in mind, we first need to handle the number. At #1, we initialize a 
buffer to receive the bytes and a number variable.

When we reach #2, we iterate and consume every following character until we 
reach a colon. When 'x' is a colon and triggers its case, we parse the buffer
into an integer and assign the number variable to that and break out of the
loop.

If x is not a colon, we push the character into the buffer. 

The last case denotes that anything else should return an error.

Moving to #3, we can handle the string portion by reinitializing our buffer.
And creating a loop that will run for as long as it is less than the number 
we just created previously. 

The loop will continue to consume the next character in the iterator and push 
it to the buffer.

At #4, we attempt to convert it to a string. If it is a success, we create the
appropriate token for it. If it is a failure, it means that we may be dealing
with "raw" bytes that have a different meaning.

Now we can move on to parsing lists! 

    fn parse_list<I>(iter: &mut Peekable<I>) -> Result<Token, String> 
        where 
            I: Iterator<Item = u8>
    {
        // #1
        let mut list = Vec::new();
        iter.next();
        // #2
        while let Some(x) = iter.peek() {
            if *x == b'e' {
                return Ok(Token::List(list)); 
            } else {
                let token = parse(iter); 
                list.push(token.unwrap());
            }
        }
    
        Err(format!("List was not built properly!"))
    }

At #1, we are creating a list that we will craft our token with. Since we 
already know that the next character in the iterator is an 'l', we can consume
the character and begin the logic of this function.

At #2, we create a loop that allows us to peek the next character in the 
iterator. From there, we check to see if the next character is the terminating
character. If it is, we craft our token and return it. If it isn't, we 
initialize a variable called token and call the root "parse()" function. 

This means that the flow will start all over again from the top. The idea is
that the elements should be either strings or integers. At the end of these 
procedures we should get either a String token or an Integer token. From there,
we will push the value inside the token to the list we created at the start 
of the function.

Now we can move on to the final rule, the dictionary. 

    fn parse_dict<I>(iter: &mut Peekable<I>) -> Result<Token, String> 
        where 
            I: Iterator<Item = u8>
    {
        let mut dict = HashMap::new();
        let mut buffer = String::new();
        iter.next();
        while let Some(x) = iter.peek() {
            if *x == b'e' {
                return Ok(Token::Dict(dict));
            } else { 
                let key = parse(iter); 
                let value = parse(iter);
    
                if let Ok(Token::Str(y)) = key {
                    dict.insert(y, value.unwrap());
                } else {
                    todo!()
                }
            }
    
        }
        Err(format!("Dict was not built properly!"))
    }

This follows the same logic of the list function. Some differences are the 
usage of a HashMap instead of a Vector. In the condition where we don't meet
the termination character, we call the parsing function twice to initialize 
our key and its value. 

We then need to ensure that the key is a string, which
we do in the next conditional. If it is we insert the key/value pair into the 
HashMap at the start of the function.
        
And that is the entirety of the decoder. It's not the best looking, but it gets
the job done for the project that I'm working on. 

I got to learn a lot of cool stuff on syntax trees and was also recommended a
"book with a Dragon on it". I will probably check it out sometime soon.
