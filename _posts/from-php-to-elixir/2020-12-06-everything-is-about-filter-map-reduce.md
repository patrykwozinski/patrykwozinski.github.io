---
layout: post
title: Everything is about filter, map and reduce
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [elixir, from-php-to-elixir, fp]
comments: true
share-img: "/img/blog/alchemy-5.jpg"
---

Hi guys! I should apologize to you for that I'm not writing too often. It's because Autumn is not my favorite time of the year; meh.

I'm after my first quarter worked as Elixir engineer. Does it was easy for me? No. Changing language from PHP to Java? Why not! PHP to C#? Let's go for it! But learning functional concepts is something more than changing your daily toolset from the wood hammer into the metal hammer. It's about **how are you looking at the problems from a higher level**.

In object-oriented languages, we think about the data encapsulated in the objects. They are connected by relations that describe the world around us. Sounds reasonable I hope.

In functional programming, **there is no more data in the heart of the problem**. There you need to think about the problem as to **how to transform something** into a different thing. 

Let's assume you’re working on an application where the main problem is to prepare a salad for your family. You have a few vegetables like tomato, cucumber, and red pepper. At first, you need to peel the cucumber. So... How to do that? You need to filter vegetables (you don't want to peel the pepper, trust me) to find cucumber, map it to take off the skin. Viola! The cucumber has been peeled! The code could look like below:
```elixir
vegetables |> filter(cucumber) |> map(&peel/1)
```

Okay, so now you have to slice all the vegetables like in the example, just without filtering because you want to do that for all the vegetables.
After everything, you would like to mix all these things into a tasty salad. How to do that functionally?
```elixir
prepared_vegetables |> reduce(&mix/2)
```

So... As you see the most important is to understand operations like **filter**, **map**, and **reduce**. Look - we’ve never worried about the state or something - the only significant operations are input transformations. Of course, **functional programming also has tons of academic terms** but you don’t need them to start your journey with FP!

I wrote this post as a short note for myself from the future. I hope I’ll find more motivation to write something more technical in the near time. I'm often posting my thoughts and interesting links that I found on [Twitter](twitter.com/patrykwozinski).
