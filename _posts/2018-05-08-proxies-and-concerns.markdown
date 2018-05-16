---
layout: default
title:  "Proxies & Cross-cutting concerns"
date:   2018-05-08 11:07:02 +1100
categories: Java
---

# [](#header-1) What is a Java Proxy?

Firstly, what does a _proxy_ mean? When we naturally think of synonyms for the word
_proxy_, we can come up with words such as: "substitute", "representative", "intermediary" etc.
These words can similarly express what a proxy in Java is as well, it is merely a Java object,
that we construct to act as an "intermediary" between a callee and a caller. ***However*** a
big difference, is that the caller does not know about the proxy, the caller
believes it is invoking the methods of the callee, but in reality, it's actually calling the proxy first,
and then the proxy will call the callee on behalf of the caller.

Let's say I have the following Map which contains a key-value pair of
names and post-codes (this will be the callee):

```
class People {
    Map<String, String> peopleAndPostcodes;

    public People(Map<String, String> peopleAndPostcodes) {
        this.peopleAndPostcodes = peopleAndPostcodes;
    }
}
```

Say we want to measure how many times a particular post-code is called, we can
introduce a proxy to measure this for us:

```
Map mapProxy = (Map) Proxy.newProxyInstance(
    ClassLoader.getSystemClassLoader(),
    new Class[] {Map.class},
    new MeasurePostcodeInvocationHandler(new HashMap<>())
)

People people = new People(mapProxy);
```

There are three parameters within that method call that we need to understand,
the first is ***ClassLoader.getSystemClassLoader***, a class loader is responsible for
for loading Java classes during runtime dynamically to the JVM. It allows us to actually
create objects of that given class, by passing in the class definition to the JVM.

The second is ***new Class[] {Map.class}***, this is a reference to the interface/ array of interfaces in
which our concrete class is implementing (as such for creating proxies, you will
need an interface representing the concrete class).

The third is ***new MeasurePostcodeInvocationHandler***, this is a reference to an object that implements
the interface _InvocationHandler_, and is responsible for doing the _behind-the-scenes_ work,
in this case, measuring how many times a post-code is used. In addition, our InvocationHandler object is usually instantiated with a concrete object; the callee. (This is because we still need to invoke those
original methods from the proxy.)

```
class MeasurePostcodeInvocationHandler implements InvocationHandler {

    private int numberOfTimesPostcode3122IsCalled;
    private final Object target;

    public MeasurePostcodeInvocationHandler(Object target) {
        numberOfTimesPostcode3122IsCalled = 0;
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) {
        Object result = method.invoke(target, args);
        if (method.getName().equals("get") && result.equals("3122")) {
            numberOfTimesPostcode3122IsCalled++;
        }
        return result;
    }
}
```

The above class is our _proxy_, whenever we invoke a method on our map object within
the "People" class, instead we will actually we calling the "invoke" method within
MeasurePostcodeInvocationHandler. Therefore, whenever we invoke the "get" method of a
Map object, and its return value is the post-code 3122, we will increment our
variable. From this, we will be able to determine how many times that post-code is
used within our code.

* * *

# [](#header-2) Why use Java Proxies?

So what's the point of actually using java proxies? Good question, most
programmers would rarely ever actually use java proxies.
Though that's not to mean you will never use it, java proxies can be a great way
of abstracting away functionality that is prevalent within many classes.
For example, you will find proxies being used heavily in frameworks, such as Spring, which implements it
for a variety of "cross-cutting concerns", such as validation, logging and security.

Thus, for "concerns/aspects" that you find prevalent throughout large parts of code,
using a proxy may be a good way to abstracting away that "concern", and allowing for
your modules to adhere to the SRP (Single responsibility principle).

* * *

# [](#header-2) Blogs for further clarification   
[http://www.baeldung.com/java-dynamic-proxies](A good read on java proxies)

* * *

# [](#header-1) What is a Cross-cutting concern?
Cross-cutting concerns are parts of a program that rely on or must affect many
other parts of the system. Cross-cutting concerns can be directly responsible for
tangling, or system inter-dependencies, within a program.

* * *




What are Java Proxies & "Cross-cutting concerns?"
-> Give examples from code
-> Talk about Spring
-> Talk about encapsulation of aspects
