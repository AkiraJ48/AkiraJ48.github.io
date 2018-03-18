---
layout: default
title:  "Functional Composition and Piping"
date:   2018-03-9 11:07:02 +1100
categories: Functional
---

# [](#header-1) Functional Composition and Piping (Javascript) (In-Progress)

* * *

## [](#header-2) What is function composition?
Most of us have mostly likely heard of the term "function composition" when it
comes to maths. For example: f(g(x)), where we apply function g onto variable x,
followed by applying function f onto the outcome of function g.  

In programming, we follow the same concept, where given some number of functions,
and some input, we apply each function onto the outcome of the previous function.
For example:

let f = (stringNumber) => { return parseInt(stringNumber) }
let g = (total, number) => { return total + number; }

where f, given a number that is a string will return it's integer version,
and g, given a total and number, will return you the value of those numbers
combined.

If we were to do: g(0, f("5")), then the outcome would give us 5.   

```
const compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);
```

const getSumOfStringNumbers = compose(g, f);


* * *

## [](#header-2) What is piping?

* * *

```
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);
```

* * *

Useful References:   
1. [What is function compostion - medium](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0)   
2. [Implementing function composition - medium](https://medium.com/@gigobyte/implementing-the-function-composition-operator-in-javascript-e2c4f1847d6a)  
