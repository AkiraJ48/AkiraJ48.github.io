---
layout: default
title:  "Dependency Injections"
date:   2018-03-07 11:07:02 +1100
categories: Design
---

# [](#header-1)Dependency Injections & Inversion of Control

* * *

## [](#header-2) Dependencies
What is a dependency? The best way to show this is through an code example:

```
public void CalculateTaxFromIncome(int income) {
    TaxRules taxRules = new TaxRules();
    int taxDeduction = taxRules.calculateTax(income);
}
```

In the above code, _taxRules_ is a dependency, that is, an object that is required
in order for the function _CalculateTaxFromIncome_ to work. Without _taxRules_, the function
will not be able to operate. Thus we say that _taxRules_ is a dependency.

* * *

## [](#header-2) Inversion of Control (IoC)
So, how is inversion of control related to dependency injections. Lets take a look
at the below code example.

```
public void CalculateTaxFromIncome(int income, TaxRules taxRules) {
    int taxDeduction = taxRules.calculateTax(income);
}
```

Instead of giving the function _CalculateTaxFromIncome_ control over instantiating
_taxRules_, we instead leave that responsibility to the class that calls _CalculateTaxFromIncome_.
Instead we are thereby ***injecting the dependency*** into the function. From this, we can show that
we are inverting the creation/control of the dependency.


* * *

## [](#header-2) Why IoC & Dependency Injections?
There's several reasons why we attempt to use IoC and Dependency Injections.
1. Agglomerate the creation of all necessary objects into a single point (file). By doing
this, it becomes easier to handle changes that may occur further down the track.
2. Typically, when we don't use dependency injections and IoC, unit testing becomes
a lot more difficult, as our classes are likely to be more closely coupled. 

* * *
