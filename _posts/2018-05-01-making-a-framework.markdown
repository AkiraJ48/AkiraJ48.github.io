---
layout: default
title:  "Building a Framework"
date:   2018-03-07 11:07:02 +1100
categories: Java
---

# [](#header-1) Building a Web Framework in Java
When one thinks of what a web framework should offer to developers,
the number 1 feature that is necessary is request mapping; given a
request to some url by a client, that should be mapped to some method
that has been defined by the developer. Today that's what we'll be looking at,
how request mapping works, and how we can use Annotations in Java as a means
to narrow our mapping.

* * *

## [](#header-2) Request Mapping
***Request Mapping*** is the process of using the information sent by a client,
such as the URL, the Request Status (GET/POST etc), body content etc, as a
means to identify which method/function we should invoke on behalf of the
developer.   

***How do we actually create that mapping?***  
To do so, we need to create a set of useable tools that allow the developer to
create a link between their own method, and what we've defined as necessary
in mapping their method to a URL. In Java, one way we can do this is through
Reflection and Annotations.  

* * *

## [](#header-2) Reflection
Reflection in Java is simply a library that allows us to introspect/examine our own
program. For example:   
```
    Class c = Class.forName(args[0]);
    Method m[] = c.getDeclaredMethods();
```

Here we are retrieving a class with some given name, and then retrieving a list
of all declared (public) methods within that class type.

* * *

## [](#header-2) Annotations
Annotations in Java basically represent a "tag" in which we can apply to classes,
packages, methods, fields etc. For example:   

```
@RequestMapping(path = "/", requestType = HttpRequests.GET)
public Object getHelloWorld() {
        return "Hello World";
}
```

Here we have the Annotation "RequestMapping", which has 2 parameters, a path and
a request type. With this annotation, we can allow the developer to define which
url path and which request type is necessary in order for this method "getHome" to be
invoked.   

* * *

## [](#header-2) Back to building a Request Mapper
I'm sure now, you have an inkling of how we can use both Reflection and Annotations
as a way to create a request mapper for a user of our framework. For example:  

```
Class<?> class = reflections.getTypesAnnotatedWith(Controller.class);
Method methods[] = class.getDeclaredMethods();
Annotation annotation = method[0].getDeclaredAnnotation(RequestMapping.class);
...

```

Once we have the given annotation, we can then retrieve the parameters that
the developer has added (e.g. the path and request type). Once we have those parameters,
whenever a request comes in from a client that matches those parameters, we can then
invoke that given method.

* * *
