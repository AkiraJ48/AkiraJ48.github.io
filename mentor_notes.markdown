---
layout: default
title:  "Notes"
categories: MYOB
---

# [](#header-1)Things that I should be focusing my attention on
1. Java Spring & React (As my mentor's crew heavily uses both)
2. Get familiar with both Java & Javascript, making use of their
	inbuilt functions. E.g. for Javascript, get familiar with reduce, map, filter,
	function composition and piping.
3. Get familiar with common design principles, patterns and techniques that are regularly used
	e.g. Dependency Injections, Single Responsibility etc.

# [](#header-1)General Feedback/ Things to keep in mind
1. Be proficient with shortcut keys in IntelliJ.
2. With Unit testing, if the project is small enough, can instead test the project as a whole, as opposed to individual units.
3. Method/Classes/Variables/UnitTests should always be as readable and specific as possible. For example:
   --> Unit Testing should follow the convention: MethodName_Returns_Result
4. Restrict dependencies within classes (Dependency Injections)

# [](#header-1)Key Feedback from MYOB CSV Parser Challenge
1. All things from the "General Feedback"
2. Especially when dealing with numbers, look at boundary test casing.

# [](#header-1)Key Feedback from the small project (Git-Parser)
1. Directory folders should represent what is actually inside of them, and also try to follow standard conventions e.g. no __example__
2. Take a more functional approach to my code i.e. aim to replace for loops with appropriate functions, such as
	reduce, map, filter and find
3. Replace all instances of "var" with either "let" or "const" (ECMA 6)
4. Though it's good to separate files based on functionality, if the files represent a similar
	functionality and have very limited amounts of code, then it may be better to place them
	all in the same file.
5. Should implement validation for all incoming strings
