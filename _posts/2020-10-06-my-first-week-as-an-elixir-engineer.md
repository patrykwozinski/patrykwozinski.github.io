---
layout: post
title: My first week as an Elixir Engineer
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [elixir, knowledge sharing, from-php-to-elixir, fp]
comments: true
share-img: "/img/blog/alchemy-3.jpg"
---

Hi guys!
Finally, I am working as an Elixir Engineer at Zenloop. That’s a company focused on the customer experience where Elixir is a primary programming language. After my first week of work, I can say one important thing: the elixir ecosystem is not mature. Many times you’ll not find packages or libraries that can pass your expectations and you will be forced to create them from the scratch.

<p align="center">
    <img src="/img/blog/alchemy-3.jpg" alt="Alchemy"/>
</p>

I’m still while onboarding process, but thanks to my buddy I have the first real Elixir task. A few days ago I got a task to do research of libraries that supports parsing CSV and XLSX documents. That's so generic thing to do in mainstream programming languages like Java, Python, or PHP but in this case, it was not obvious. I found a few packages on the Hexdocks (that's like packaging for PHP, but it's even better) - few of them were not looking stable for me or had not the best support from the creators. **That's important when you're looking for a tool for professional usage** - not for a home project.

In my case, I’ve needed tools that help me to define the encoding of the given file and something that could guess which delimiter for the CSV file was used. There is no package, library, or tool that meets my requirements exists in the Elixir ecosystem and I’ve needed to create them from the scratch. I feel sad.

Besides missing tools that have forced me to create my own is that - the Elixir is perfect for processing sets of the data. That’s not a great discovery - but for me, it was pretty cool to see how a functional approach helps to deal with file processing in a real project. The next things that are so fancy for me are protocols and behaviours. I’m interpreting them as a type of IoC (Inversion of Control), protocols are for data and the behaviours for modules. Do you think it’s a good topic to cover in the next post?
