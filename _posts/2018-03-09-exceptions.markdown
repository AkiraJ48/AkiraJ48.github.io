---
layout: default
title:  "Exception Handling in Java"
date:   2018-03-23 11:07:02 +1100
categories: Java
---

# [](#header-1) Exceptions

* * *

## [](#header-2) ITEM 69: USE EXCEPTIONS ONLY FOR EXCEPTIONAL CONDITIONS
> Exceptions, as implied only be used exceptional instances; instances that aren't merely
your ordinary control flow.

A well-designed API must not force its clients to use exceptions for ordinary control flow.

For example, the Iterator interface has the state-dependent method next and the corresponding state-testing method hasNext.
This enables the standard idiom for iterating over a collection with a traditional
for loop (as well as the for-each loop, where the hasNext method is used internally):

```
for (Iterator<Foo> i = collection.iterator(); i.hasNext(); ) {  
    Foo foo = i.next();
    ...

}
```

On the other hand, if the Iterator interface did not have the state-testing method hasNext,
then we'd have to do the following:

```
// Do not use this hideous code for iteration over a collection!
try {
    Iterator<Foo> i = collection.iterator();
    while(true) {
        Foo foo = i.next();
        ...
    }
} catch (NoSuchElementException e) {
    ;
}
```

>In summary, exceptions are designed for exceptional conditions.
Don’t use them for ordinary control flow, and don’t write APIs that force others to do so.

* * *

## [](#header-2) ITEM 70: USE CHECKED EXCEPTIONS FOR RECOVERABLE CONDITIONS AND RUNTIME EXCEPTIONS FOR PROGRAMMING ERRORS

Java provides there kinds of throwable exceptions:
1. Checked Exceptions
2. Runtime Exceptions (Unchecked)
3. Errors (Unchecked)

> Checked Exceptions are exceptions that may occur regularly and in which the compiler will check
at compile time and ensure that if a certain method is throwing an exception, that at some point, it is
caught. Otherwise the compiler will throw an error saying "Unreported Exception". E.g. FileNotFoundException or
EndOfFileException.


> Unchecked Exceptions are exceptions in which the compiler will not check at compile time,
such as NullPointerExceptions. These typically occur during runtime, due to a given input.
There are two types of this:
    1. Runtime Exceptions
    2. Errors
They are both identical in behaviour, both are throwable that aren't needed and typically
shouldn't be caught. If a program throws an unchecked exception/error it's generally the
case that recover is impossible, and trying to continue execution may potentially do more
harm than good.


The golden rule in regards to either using a checked or unchecked exception is this:
if you can recover from the given issue, then use a checked exception.

By confronting the user with a checked exception, the API designer presents a mandate to recover from the condition.
The user can disregard the mandate by catching the exception and ignoring it, but this is usually a bad idea.
Use runtime exceptions to indicate programming errors. The great majority of runtime exceptions indicate precondition violations.
A precondition violation is simply a failure by the client of an API to adhere to the contract established by the API specification.

One issue with this given advice, is that it's not always easy to tell whether a given error/exception is easy to recover from.
For example, consider the issue of memory shortage. There may be times, where it's better to stop the program due to
rampant use of memory. For example during a DDOS attack, you may have no way of combatting consistent requests coming in,
and as a result, it'd be a lot better for your program to stop instead of trying to execute.

On the other hand, if the memory shortage was occurring due to bad programming, i.e. allocating too much memory
to an array, then you may want to try and recover from that, because it's more easily dealt with and
recoverable from.

If you believe a condition is likely to allow for recover, use a checked exception; if not, use a runtime exception.
If it's not clear, than you're probably better off using an unchecked exception.

To summarize, throw checked exceptions for recoverable conditions and unchecked exceptions for programming errors.
When in doubt, throw unchecked exceptions. Don’t define any throwables that are neither checked exceptions nor runtime exceptions.
Provide methods on your checked exceptions to aid in recovery.

* * *

## [](#header-2) ITEM 71: AVOID UNNECESSARY USE OF CHECKED EXCEPTIONS

A great way to eliminate a checked exception is to return an optional of the desired result type.
For example: returning a boolean that expresses whether the action can occur or not.
The disadvantage of this, is that we can't return additional information detailing it's inability to
perform said action. Exceptions on the other hand, have the capability of expressing what
went wrong, and additional information.

For example:
```
try {
    object.action(args)
} catch (CheckedException e) {
    ..// Handle Exception
}

```
Instead of the above, we can split the code into two operations :
```
if (object.canDoAction(args)) {
    object.action(args);
} else {
    ..// Handle Exception
}

class object {
    function boolean canDoAction(args) {
        try {
            // Merely attempt the action, we don't actually want it to produce/affect any outcome
            ableToDoAction(args);
            return true;
        } catch (CheckedException e) {
            return false;
        }
    }
}
```

This refactoring may not always be possible, but if we can, it does make the given code
a lot more pleasant to use.

In summary, when used sparingly, checked exceptions can increase the reliability of programs;
when overused, they make APIs painful to use. If callers won’t be able to recover from failures, throw unchecked exceptions.
If recovery may be possible and you want to force callers to handle exceptional conditions, first consider returning an optional.
Only if this would provide insufficient information in the case of failure should you throw a checked exception.


* * *

## [](#header-2) ITEM 72: FAVOR THE USE OF STANDARD EXCEPTIONS

* * *

## [](#header-2) ITEM 73: THROW EXCEPTIONS APPROPRIATE TO THE ABSTRACTION

It is disconcerting when a method throws an exception that has no apparent connection to the task that it performs.
This often happens when a method propagates an exception thrown by a lower-level abstraction.

To avoid this problem, higher layers should catch lower-level exceptions and, in their place,
throw exceptions that can be explained in terms of the higher-level abstraction.
This idiom is known as exception translation:

```
try {
    ... // Go a level below
} catch (LowerLevelException e) {
    throw new HigherLevelException(...);
}
```

While exception translation is superior to mindless propagation of exceptions from lower layers, it should not be overused.
Where possible, the best way to deal with exceptions from lower layers is to avoid them,
by ensuring that lower-level methods succeed. Sometimes you can do this by checking the validity of
the higher-level method’s parameters before passing them on to lower layers.

In summary, if it isn’t feasible to prevent or to handle exceptions from lower layers, use exception translation,
unless the lower-level method happens to guarantee that all of its exceptions are appropriate to the higher level.
Chaining provides the best of both worlds: it allows you to throw an appropriate higher-level exception,
while capturing the underlying cause for failure analysis

* * *

## [](#header-2) ITEM 74: DOCUMENT ALL EXCEPTIONS THROWN BY EACH METHOD

Always declare checked exceptions individually, and document precisely the conditions
under which each one is thrown using the Javadoc @throws tag.
```
public void GetName() throws CannotGetNameException {
    ;
}
```

As opposed to:
```
public void GetName() throws Exception {
    ;
}
```

Use the Javadoc @throws tag to document each exception that a method can throw,
but do not use the throws keyword on unchecked exceptions.

If an exception is thrown by many methods in a class for the same reason, you can document
the exception in the class’s documentation comment rather than documenting it individually for each method.

* * *

## [](#header-2) ITEM 75: INCLUDE FAILURE-CAPTURE INFORMATION IN DETAIL MESSAGES

Whenever an exception occurs, it presents a stacktrace, detailing simple information on
the thrown exception. However, there are times when an problem is not easy to re-create,
and as such it is always critically important for that given exception to return as much
information as possible, concerning the cause of the failure. In other words, the given
message of an exception should capture the failure for subsequent analysis.

To capture a failure, the detail message of an exception should contain the values of all parameters
and fields that contributed to the exception. For example, the detail message of an IndexOutOfBoundsException
should contain the lower bound, the upper bound, and the index value that failed to lie between the bounds.

One caveat concerns security-sensitive information. Because stack traces may be seen by many people in the
process of diagnosing and fixing software issues, do not include passwords, encryption keys, and the like in detail messages.

* * *

## [](#header-2) ITEM 76: STRIVE FOR FAILURE ATOMICITY

After an object throws an exception, it is generally desirable that the object still be in a well-defined, usable state,
even if the failure occurred in the midst of performing an operation. This is especially true for checked exceptions,
from which the caller is expected to recover. Generally speaking, a failed method invocation
should leave the object in the state that it was in prior to the invocation. A method with this property is said to be ***failure-atomic***.

There are several ways to achieve this effect. The simplest is to design immutable objects.
If an object is immutable, failure atomicity is free. If an operation fails, it may prevent a
new object from getting created, but it will never leave an existing object in an inconsistent state, because
the state of each object is consistent when it is created and can’t be modified thereafter.

For methods that operate on mutable objects, the most common way to achieve failure atomicity is to check
parameters for validity before performing the operation. This causes most exceptions to get thrown before
object modification commences. For example, consider the Stack.pop method:

```
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();

    Object result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference
    return result;

}
```

A third approach to achieving failure atomicity is to perform the operation on a
temporary copy of the object and to replace the contents of the object with the
temporary copy once the operation is complete.

A last and far less common approach to achieving failure atomicity is to write recovery code that
intercepts a failure that occurs in the midst of an operation, and causes the object to roll back its

In summary, as a rule, any generated exception that is part of a method’s specification should leave
the object in the same state it was in prior to the method invocation. Where this rule is violated,
the API documentation should clearly indicate what state the object will be left in.

* * *

## [](#header-2) ITEM 77: DON’T IGNORE EXCEPTIONS

An empty catch block defeats the purpose of exceptions.
If you choose to ignore an exception, the catch block should contain a comment
explaining why it is appropriate to do so, and the variable should be named ignored:

```
Future<Integer> f = exec.submit(planarMap::chromaticNumber);
int numColors = 4; // Default; guaranteed sufficient for any map

try {
    numColors = f.get(1L, TimeUnit.SECONDS);
} catch (TimeoutException | ExecutionException ignored) {
    // Use default: minimal coloring is desirable, not required
}
```




* * *
