---
layout: post
title: Static classes — the home of the devil?
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [oop, good practices]
comments: true
---

First, static classes don’t really tell us how they work and what dependencies they have. We’re never sure what they’re hiding inside. Second, don’t they remind you of the procedural approach to programming? Exactly! And we want to be ‘object thinking’ like David West, don’t we?! In fact, static classes will never be objects, because we don’t create them. They simply operate on the data that we pass to the methods, then they follow the instructions. Another problem is the fact that static methods hide dependencies between individual objects. We find out about the use of other classes only by looking deeply at the implementations in which they were used; we’re not able to find out about them by looking at the constructor itself. The problem grows with increased cyclomatic complexity of our static methods, because we have no idea what will happen somewhere in the depths of our hidden dependence.

<p align="center">
    <img src="https://miro.medium.com/max/700/0*t5oipwpTCYh53MFy" alt="The programmer"/>
</p>

### And what about auxiliary classes, helpers and other utils?
Such a question is surely asked by a large group of people who hear that classes filled with static methods are entities that are inconsistent with the object-oriented approach. After all, thanks to the fact that they are static, we are able to call them from anywhere in the application and, more importantly, from any context.

Instead of building a non-specific class with 30 methods (which number is constantly growing btw), maybe it would be better to prepare an interface that will define specific behaviours and then implement them as separate classes with a given responsibility one by one? The single responsibility principle is thus met, and we can expand our new ‘helpers’ without building (to quote Sławomir Sobótka) eight-thousanders with dozens of static methods. In addition, after such a change, we have objects and we have their behaviour! We can finally treat them as living objects, juhu!

### Static factories and creating exceptions?
Static factories are a very problematic topic. Due to the fact that we are not able to create their pure relationship with other objects (for example UserRepository), we have no control over how we check the correctness of the object being created. Therefore, the sense of creating such factories disappears, since they should be used to build more complex objects, where an additional layer of validation is useful. I also think that building facilities in factories is a great solution and the possibility of avoiding unnecessary keywords ‘new’, but only when we use factories that are ‘real objects’ :)

What remains is the question of specific factories and creating objects of exceptions that we use in specific layers of applications. I personally use these only where classes contain mostly static methods. Why? It is much easier to call an exception in a specific context by building it from the level of the static method rather than properly preparing the object in each call. In addition, the readability of such methods is really a plus — when we know the limits in which we can create new methods, we can easily separate sensitive and correct methods from those that create the so-called divine class.

### Let’s look at a simple exception:
`ProductNotFound::forCatalog(catalog)` - here we have a specific reason for the failure, as well as the context to which it relates. It’s very convenient! An additional advantage is the consistency of messages passed in exceptions. We do not have to supplement them with the implementation; we just need to use a simple static method to give us the context to have all the information we need.

### What about unit tests?
Here is another problem arising from the use of statics. It is very difficult to test the class code on a unit that refers to a different place in the application using a static method. Something like this is not possible to mock up, which might make the unit tests stop being what they are, since we might not be able to dynamically replace a specific implementation. This is, in my opinion, a very large limitation, and it is worth considering a change to a more object-oriented approach.

### So? Hell of static classes?
I think i will end this story with a classic ‘it depends’ answer — I’ll leave the interpretation to you. Personally, I think that thanks to a deeper analysis of the demand for a method or a static class, we are able to learn a lot and improve the quality of the applications we create. Remember that we lie when typing ‘object-oriented programming’ on our CV when we continue to use the procedural approach to programming.
