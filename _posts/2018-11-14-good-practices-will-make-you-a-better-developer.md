---
layout: post
title: Good practices will make you a better developer
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [junior, good practices, refactoring]
comments: true
---

What are good practices? These are solutions that have been proven to work many times by people who have been programming longer than some of us live. People who cut their teeth on Cobol burst out laughing when they see the next NullPointerException and write more CRUDs than we can imagine — they know perfectly well what rules and habits are able to facilitate our work. Books written by legends that refer to the principles of object-oriented programming, which already dominated in the ’80s, are timeless and reading them can really make us better programmers/engineers.

> I’m not a great programmer; I’m a good programmer with great habits. – Kent Beck

<p align="center">
    <img src="https://miro.medium.com/max/700/0*frTvs-_mPMuG2yZU" alt="The programmer"/>
</p>

In this story I will discuss the cool practices related to both programming and soft skills that are worth developing and improving.

### Code Review (CR)
I consider the process of reviewing the code to be absolutely mandatory. No piece of code should go anywhere outside your local environment without a CR. It is irrelevant whether one developer takes part in the process, or four. It is important that the code you wrote is reviewed and — crucially — understood by other people. Not everyone can fit to the role of the programmer writing a specific feature, but the general verification of the correctness of new pieces of application should be a natural step in the development phase.

It is worth opening the CR after the first sensitive commit, which already adds some value. Thanks to this, other developers will see how you think and develop your branch. In addition, it will be much easier for them to understand the problem you are trying to solve when you give them the option of ‘sampling’ the code in smaller portions.

Never accept code that you don’t understand. It’s not about teasing your team/project mate, but about the fact that one of the important advantages of CR is learning from each other. The reader learns from the writer and vice versa. By accepting changes that you do not understand, you become the loser. It is your ‘green light’ that will be responsible for new changes that might destroy the environment and cause errors. If you have trouble verifying the correctness of any code, let the author know — sit with them and talk about the fragments that are difficult for you to understand. There is a great possibility that the feature has been written incorrectly. In the development world some years ago, we used to call someone a ‘senior’ when they wrote 5 lines of code that no one else understood. Today it is unimaginable — code has to be like a good book — you have to write it in such a way that other people enjoy reading it ;) Mathias Verraes added to this subject when he said that a merge is the responsibility of the entire team and should not occur if even one developer disagrees or is unable to understand the changes.

Another good practice in terms of CR is taking it seriously. We all know it takes a while, but it is part of our work. Before you start working on a new task, view the notifications from GitHub, GitLab or other BitBucket and take some time to review the code to which you have been assigned. It’s improving the work of other people who will in this case get their feedback faster. In addition, if you work in an agile way, you’ll know what’s going on in the team and be on track with the work progress.

Tests, tests, tests — while some emphasize their importance, others can no longer bear to hear about them. However, when a pull request relates to a functionality that is relatively important and will be used in many places of application, it is worth considering whether the author should add tests. Of course, this applies when they’re not working with the TDD approach. Adding a unit of tests takes just a moment, and can save your as* — then keep it unkicked if someone later decides to make changes to your code.

### Culture of refactoring
An inseparable part of the programmer’s work is refactoring existing code. I love the Scouts principle: ‘Always leave the campground cleaner than you found it.’ When it comes to refactoring, we all know the topic well, everyone related to simplifying code talks about it, but do we really care?

The importance of clean code might be very problematic to explain to the business department in your company. You might hear that nobody wants to pay for it, because it doesn’t bring any benefits to the product.

It is worth starting refactoring right where we are, gently correcting variable names, shifting and eliminating unnecessary complexities in the code. It is a very subtle but powerful way of improving the readability of any code change. Let’s just clean around us, though; don’t dig too deep. If we see that a bigger piece of the application is causing problems or could be solved in another (of course better!) way, let’s measure the impact of the changes then convert it into business values.

Accelerating the loading of element X improves conversions, though a small number of users won’t use the module for performance reasons. It is also worth measuring the frequency of editing a given fragment of the system. A great tool in PHP for such operations is Churn. It shows us information about classes that are often changed, and how high their cyclical complexity is. An excellent point is indicating the frequency of editing the class — it means that many developers visit it often, and an illegible class is a class for which maintenance is more expensive. One more thing is the suggestion that the class may be too large and may break the principle of single responsibility — each class should have only one reason to be changed. If there are many changes, we start to feel the code smell.

Refactoring is also a great (if not the best) way of learning about how applications work. To change a piece of code, you must first understand it and also understand the context in which it is working. Each subsequent action to organise the clutter in the system is an added value as a form of getting to know the application better. In addition, tests will be necessary to start larger refactoring actions. Not only can we learn a lot from the test analysis itself, but, by writing them, we can memorise the way the specific classes and the relationships between them work, too. Cool!

### Technological demos
In DocPlanner we organise regular technology demos. This is another very nice way to learn and expand knowledge about applications. Such meetings are different from regular product demos because they are focused on topics related to technological solutions used in the product. This is where developers, administrators and other IT team members present their latest achievements and discoveries. These are great because the person who prepares the presentation is able to visualise the achieved goals and the listeners can find out what is happening in other teams. The scope of these team tasks is often completely strange to the demo participants, so tech demos show the speaker’s challenges and the possible common points between the projects on which both they and the participants are working. The added value from this is also that a rotation of developers between teams is easier, when the need arises. Tech demos often have a much more informal character than product demos. Everyone feels free to ask questions, which can result in interesting discussions on tech solutions.

### StackOverflow from the other side?
StackOverflow is the place we turn to when we encounter a barrier during development and we need help. Most often, you end up searching for similar problems, since many believe that if your problem is not on Stack, it does not exist. In my opinion, it is worth creating an account and adding selected tags to the topics in which we feel strong. The next step is to search for topics in which we could help others. Of course, many questions are asked without consideration, but often we can find very interesting problems. By helping other users, we learn as well, since often there are cases that we do not face at work, allowing us to ‘broaden our horizons’. It all works just like a karate kata (coding dojo) — a series of repeated exercises allowing us to strive for perfection. Sometimes mistakes and user problems are repeatable, and sometimes we do encounter them at work, but by helping others, we consolidate our solutions anyway. Thanks to this, when a less experienced programmer approaches us later, we will be able to lead him to a solution much faster and also suggest various options.

### Active participation in open-source projects
Developing an application with open code that is available to everyone is another way to gain experience that can turn out to be needed in your workplace. While contributing to other (open-source) projects, we not only learn a lot, we also show ourselves as experts in specific topics. This is tangible proof that we understand and know external tools. An additional plus is the ability to implement the changes that we care about the most and that will solve OUR problems. It is also beneficial to contribute to reducing the implementation time of the new version of the project, which we use on a daily basis.

<hr>

The work of a professional developer is associated with constant learning of both technological innovations and soft skills. Kent’s words perfectly describe a real specialist — they have great habits that make them noticeable in the devs community.
