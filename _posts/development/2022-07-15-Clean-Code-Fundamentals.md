---
layout: post
title: Design pattern
author: Faten Elhariry
categories: Dev
tags: Design pattern, Dev
---
## this will be a summary for the video series [clean code](https://learning.oreilly.com/videos/clean-code-fundamentals/9780134661742/)
### Design pattern
#### Observer Pattern
  - = Publishers + Subscribers
  - <img src="/images/f0045-01.png" />
  - A Duck object comes along and tells the Subject that he wants to become an observer.
  - defines a one-to-many dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically.
  - The Observer Pattern defines a one-to-many relationship between a set of objects.
  - When the state of one object changes, all of its dependents are notified.

#### decorator design pattern 
  - if you use inhirtance you will end up with static code and if you implement new class for each type you will end up with explosiore for number of classes that you have 
  - so decorator work as a wrapper where you can wrap your object inside another object it enable you to enforce your code dynamically and at runtime
  <img src="/images/dev/DP/1.png" />  

#### Factory desgin pattern
  - Factory Method Pattern it is an abstract level to define the creation of the class 
  - gives us a way to encapsulate the instantiations of concrete types
  - the Factory Method pattern lets subclasses decide which class to instantiate.
  - Because the Creator class is written without knowledge of the actual products that will be created, we say “decide” not because the pattern allows subclasses themselves to decide, but rather, because the decision actually comes down to which subclass is used to create the product.

#### The Dependency Inversion Principle
  
#### One-of-a-Kind Objects: The Singleton Pattern