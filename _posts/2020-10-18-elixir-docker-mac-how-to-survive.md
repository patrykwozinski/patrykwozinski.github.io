--
layout: post
title: Elixir, Docker, MacOS - how to survive?
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [elixir, docker, from-php-to-elixir, macos]
comments: true
share-img: "/img/blog/alchemy-4.jpg"
---

## Let's talk about the Docker on Mac
Maybe you were thinking about using Docker for your development, but... meh, it doesn't work as you want on MacOS. It's so slow and really hurts when you're trying to compile application. I know that - I had the same before when I was creating applications using PHP language. Okay, let's start from the beginning - what is that pain in the ass?

### Why the Docker is so slow with MacOS?
I don't want to present myself as an expert in this topic so the best would be if I just put there the best description that I found.
<description why it's slow>

## Ok, so what should I do?

### Use Docker Edge
The first one thing that you could do is to use Docker Edge for Mac - not the stable version. I know - it doesn't sound amazing, but please trust me - it will speed up your environment. Edge version uses gRPC-FUSE as default and that's the magic which improves the performance.

### Prepare your Docker settings
There you have a few things to do in your Docker's settings. The first one is to increase resources. Probably you need more CPUs and more RAM dedicated to your application. Of course it depends on the size of your project, but let's guess it's something bigger than simple CMS system. ;)

### Share only necessary resources
You know, sharing too many directories with your Docker machine is not the best idea if you need to improve the performance.

### Use Mutagen.io
Mutagen.io is a great tool that would speed up files synchronization between the host (your computer) and the container (docker).
