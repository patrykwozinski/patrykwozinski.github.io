---
layout: post
title: Symfony Messenger component for CQRS applications
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [php, symfony, cqrs]
comments: true
---

# Symfony Messenger component for CQRS applications
Hi there! This article is mostly for people who, like me some time ago, are looking for information on how to configure Messenger for applications based on **CQRS architectural pattern**. To be honest, Messenger is my favorite SF component.

So at first — what is a CQRS? It’s the acronym of **Command-Query Segregation Responsibility**. In simple words — you’re split write-model from the read-model. That’s an approach first described by **Greg Young**.
**Read-Model** is the part of your application that is reading (wow?!) something from the source (eg. database, files, etc.). Read-model is not mutating the state of a system.
**Write-Model** is a part of your app that is mutating the state, for example via deleting resources, updating, or creating new ones. It’s not returning anything — just changing the resources.

I recommended you to read **Martin Fowler’s** [post](https://martinfowler.com/bliki/CQRS.html).

Of course, you can “do CQRS” using separate databases per write/read model or either different database systems per write/read. Remember — that’s unnecessary in all the cases. Please treat CQRS as a tool. It’s just a hammer, and you’re deciding if it needs a wood grip or steel is enough.

At the point — in this post, I’d like to describe how I’m managing the configuration of the **Symfony Messenger** component to work with a pure CQRS system.

<p align="center">
    <img src="https://miro.medium.com/max/1000/1*EfLEyiTbsz5J_eVJ6ngokg.png" alt="Coffee"/>
</p>

## Some abstraction of Messenger is always welcome
You know, using directly Symfony components it’s not the best approach that you can find. When I’m starting from scratch with a new project — I’m just creating a thin layer between my application code and the infrastructure elements like Symfony Messenger or any other dependency. That’s just a good habit to have dependency inverted (using some small abstraction) in places like this one.

Let’s start from including new dependency of our application via composer simply running the following command:
```sh
composer require messenger
```

### Command building blocks
In my example, I have a common package with CQRS component elements and there are three interfaces responsible for commands like the following:
```php
<?php 

declare(strict_types=1);

namespace App\Common\CQRS;

interface Command
{
}

interface CommandBus
{
    public function dispatch(Command $command): void;
}

interface CommandHandler
{
}

```

**Command** and **CommandHandler** are just empty interfaces that are defining functionalities. **CommandBus** interface is a place where the DI lives. Thanks to this we can in simply way invert the dependencies. After all, we have an implementation that is using the Messenger component. **Remember: you should NEVER use directly the Messenger implementation of the CommandBus.**
```php
<?php

declare(strict_types=1);

namespace App\Common\CQRS;

use Symfony\Component\Messenger\MessageBusInterface;

final class MessengerCommandBus implements CommandBus
{
    private MessageBusInterface $commandBus;

    public function __construct(MessageBusInterface $commandBus)
    {
        $this->commandBus = $commandBus;
    }

    public function dispatch(Command $command): void
    {
        $this->commandBus->dispatch($command);
    }
}
```

As you see the name of the `MessageBusInterface` parameter is `$commandBus` — it covers the dedicated bus to handle your command messages. We’ll cover this topic later.


### Query building blocks
Yeah! We already have a “C” from CQRS done. It’s time to prepare some queries. To do this we need another three interfaces — very similar to the above.
```php
<?php

declare(strict_types=1);

interface Query
{
}

interface QueryBus
{
    /** @return mixed */
    public function handle(Query $query);
}

interface QueryHandler
{
}
```
**Query** and **QueryHandler** are without any declared methods. And **QueryBus** is similar to CommandBus - place where is the point of dependency inversion and abstraction of Messenger. Another reminder — **don’t use directly the Messenger implementation of QueryBus, do it only via QueryBus interface**. Look at the MessageBusInterface parameter — the name is `$queryBus` because it indicates name of the bus (`query.bus`).
