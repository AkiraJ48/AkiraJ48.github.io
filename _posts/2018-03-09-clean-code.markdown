---
layout: default
title:  "Clean Code"
date:   2018-03-09 11:07:02 +1100
categories: Books
---

# [](#header-1)Clean Code

* * *

## [](#header-2) Introduction Notes
> The only valid measurement of code quality: WTFs/minute

* * *

## [](#header-2) Chapter 1

> Remember that code is really the language in which we ultimately express the requirements.
We may create languages that are closer to the requirements. We may create tools that help us
parse and assemble those requirements into formal structures.
But we will never eliminate necessary precision—so there will always be code. (Page 33)

***The Total Cost of Owning a Mess***
> Every change they make to the code breaks two or three other parts of the code. No change is trivial.
As the mess builds, the productivity of the team continues to decrease, asymptotically approaching zero.

***The Grand Redesign in the Sky***
> Because the old design is horrendous, people want to re-create it.

***Attitude***
> We are unprofessional.
The managers and marketers look to us for the information they need to make promises and commitments;
and even when they don’t look to us, we should not be shy about telling them what we think.
So too it is unprofessional for programmers to bend to the will of managers who don’t understand the risks of making messes.

***The Primal Conundrum***
> The only way to make the deadline—the only way to go fast—is to keep the code as clean as possible at all times.

***The Art of Clean Code?***
> Writing clean code requires the disciplined use of a myriad little techniques applied through a painstakingly acquired sense of “cleanliness.”

***What is Clean Code?***
> Clean code should be pleasing to read.  
> Bad code tempts the mess to grow!   
> Error handing should be complete.   
> Clean code is focused.   
> Our code should be matter-of-fact as opposed to speculative. It should contain only what is necessary.   
> Clean code makes it easy for other people to enhance it.   
> Smaller is better.   

***Schools of Thought***

***We are Authors***
> We are constantly reading old code as part of the effort to write new code.

* * *

## [](#header-2) Chapter 2 - Meaningful Names

***Use Intention-Revealing Names***
>The problem isn’t the simplicity of the code but the implicity of the code (to coin a phrase): the degree to which the context is not explicit in the code itself.

***Avoid Disinformation***
>Spelling similar concepts similarly is information. Using inconsistent spellings is dis- information.

***Make Meaninigful Distinctions***
>Number-series naming (a1, a2, .. aN) is the opposite of intentional naming. Such names are not disinformative—they are noninformative; they provide no clue to the author’s intention.
>Imagine that you have a Product class. If you have another called ProductInfo or ProductData, you have made the names dif- ferent without making them mean anything different.

***Use Pronounceable Names***
>If you can’t pronounce it, you can’t discuss it without sounding like an idiot

***Use Searchable Names***
>Don't use single letter variables i.e. 'e'

***Avoid Encodings***

***Hungarian Notation***
>E.g. PhoneNumber phoneString;

***Member Prefixes***
> Don't do m_something ...

***Interfaces and Implementations***
> For interfaces try not to do IInterface (don't know why)

***Avoid Mental Mapping***
>Readers shouldn’t have to mentally translate your names into other names they already know.
E.g. using a as a variable name, people may have to map it something like accountName etc.

***Class Names***
> Should be a noun phrase

***Method Names***
> Should be a verb phrase

***Don't be cute***
> Say what you mean. Mean what you say.
E.g. don't use whack() just use kill()

***Pick One Word per Concept***
> If you use multiple connotations of something e.g. fetch, get, retrieve and they all
do the same thing, then just use one word instead. It gets annoying. Same with controllers
managers and drivers.

***Don't Pun***


***Use Solution Domain Names***
> People who read your code will be programmers. So go ahead and use technical terms (
e.g. algorithms, patterns, maths etc.
)

***Use Problem Domain Names***
> Use words in the domain e.g. accounting - invoice

***Add Meaningful Context***
> Add context to variables by closing them within a function or class

***Don’t Add Gratuitous Context***
> In an imaginary application called “Gas Station Deluxe,” it is a bad idea to prefix every class with GSD.
