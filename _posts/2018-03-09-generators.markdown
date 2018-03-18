---
layout: default
title:  "Generators"
date:   2018-03-9 11:07:02 +1100
categories: Functional
---

# [](#header-1) Generator Functions

***What is a generator function?***
The easiest way to think of a generator function, is to imagine that it is an
iterator; an object that can be looped upon ( e.g. [1,2,3,4] ).

***What's the point of a generator function if we already have loops?***
This is what I struggled with the most, what's the point of using this complex
iterator if arrays or recursion already exist? Lets look at an example, that
involves getting the next fibonnaci number in three differing ways.

<strong>Recursive</strong>

```
function recursive_fibonacci_number(int number):
    if number == 0:
        return 0;
    else if number == 1:
        return 1;
    return recursive_fibonacci_number(number - 1) + recursive_fibonacci_number(number - 2)
```

<strong>Storing fibonnaci numbers into an array</strong>

```
int[] fibonnaci_numbers_storage = [0, 1, 1, 2, 3, 5, 8.....]
function add_next_fibonacci_number():
    fibonnaci_numbers_storage[next_number] = fibonnaci_numbers_storage[last_number] + fibonnaci_numbers_storage[second_last_number]

function get_fibonacci_number():
    return fibonnaci_numbers_storage[last_number]
```

<strong>Generator</strong>

```
function get_fibonacci_number():
    a = 0, b = 1
    while True:
        yield a
        a, b = b, a + b

fib_number = get_fibonacci_sequence()
next_fib_number = fib_number.next()    
```

I've demonstrated 3 ways in which we can tackle the fibonnaci problem (Getting the
next fibonnaci number).
1. The first method involves using recursion to retrieve the fibonnaci number.
2. The second method involves using an array to store the values of the fibonnaci number.
3. The third method involves using generators to retrieve the fibonnaci number.

For the second example, we're directly storing our numbers into an array. The first example is even worse,
when we call our function, our stack size becomes exponential. Our generator on the other
hand, whilst still using some memory, only requires memory on the current state of
the function.

***So how does the generator actually work?***
A generator function is typically defined by its usage of the _yield_ command as opposed
to the "return" command. _yield_ similarly will return a value back to the caller of
the function, however, unlike return, when the function is called once again, the
function will start from the line after _yield_ was called.
(This normally means that "yield" is also within a loop - though that's not always the case).
For example: when _yield_ is called in get_fibonacci_number(),

```
yield a
```

when get_fibonacci_number() is called once again via the next() function, we will start
at the line,

```
a, b = b, a + b
```

So what's happening behind the scenes, is that the current state of the function
is also being saved in memory. So when the function is called, we know the
state of all variables and the current line we are up to. This naturally means
that instead of having to save large amounts of data, or using large amounts of memory
dedicated to creating new stacks (for recursion), we have a simple saved state of our function
in which we can regularly retrieve.

References:
[Medium - How to use python generators](https://medium.freecodecamp.org/how-and-why-you-should-use-python-generators-f6fb56650888)
