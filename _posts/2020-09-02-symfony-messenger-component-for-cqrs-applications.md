---
layout: post
title: Symfony Messenger component for CQRS applications
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [php, symfony, cqrs, software architecture]
comments: true
---

Hi there! This article is mostly for people who, like me some time ago, are looking for information on how to configure Messenger for applications based on **CQRS architectural pattern**. To be honest, Messenger is my favorite SF component.

So at first — what is a CQRS? It’s the acronym of **Command-Query Segregation Responsibility**. In simple words — you’re split write-model from the read-model. That’s an approach first described by **Greg Young**.
**Read-Model** is the part of your application that is reading (wow?!) something from the source (eg. database, files, etc.). Read-model is not mutating the state of a system.
**Write-Model** is a part of your app that is mutating the state, for example via deleting resources, updating, or creating new ones. It’s not returning anything — just changing the resources.

I recommended you to read **Martin Fowler’s** [post](https://martinfowler.com/bliki/CQRS.html).

Of course, you can “do CQRS” using separate databases per write/read model or either different database systems per write/read. Remember — that’s unnecessary in all the cases. Please treat CQRS as a tool. It’s just a hammer, and you’re deciding if it needs a wood grip or steel is enough.

At the point — in this post, I’d like to describe how I’m managing the configuration of the **Symfony Messenger** component to work with a pure CQRS system.

<p align="center">
    <img src="/img/blog/symfony-messenger-component.jpg" alt="Coffee"/>
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
```php
<?php

declare(strict_types=1);

namespace App\Common\CQRS;

use Symfony\Component\Messenger\HandleTrait;
use Symfony\Component\Messenger\MessageBusInterface;

final class MessengerQueryBus implements QueryBus
{
    use HandleTrait {
        handle as handleQuery;
    }

    public function __construct(MessageBusInterface $queryBus)
    {
        $this->messageBus = $queryBus;
    }

    /** @return mixed */
    public function handle(Query $query)
    {
        return $this->handleQuery($query);
    }
}
```

Do you see **differences between QueryBus and CommandBus implementations**? In a `MessengerQueryBus`, I’ve used `HandleTrait` because as default, buses are not returning results and this trait just makes easier fetching results from the buses. I hate that in the PHP we don’t have generic types so creating QueryBuses would be much more elegant. Meh, just dreams. In the real world, we just need to define `mixed` return type in a docblock.

## What about a configuration?
Okay, writing the code as far as I think is easy for every developer that is reading this post, and you’re here because you want to get something interesting. Configuring services is not the most exciting activity, but anyway, let’s dive into this topic.

At first, we need to define buses and their transports. Most simply, you can add these lines to your messenger YAML configuration.
```yaml
# /config/packages/messenger.yaml

framework:
    messenger:
        default_bus: command.bus

        buses:
            command.bus:
                middleware:
                    - doctrine_transaction
            query.bus: ~

        transports:
            sync: 'sync://'
            async: '%env(MESSENGER_TRANSPORT_DSN)%'

        routing:
            'App\Command\BuyItem': sync
            'App\Command\SellItem': async
            'App\Query\ListCartItems': sync

```

### Few words about the buses
As you can see we have defined two buses — one for queries and one for commands. If you’re using Doctrine you can add middleware for the command bus that is **handling the messages transactional** so thanks to this you don’t need to do any mess in your command handlers. :) Just assign `doctrine_transaction` middleware and that’s all. You can also define there default middleware for enabling the option to create buses that don’t have any handler of some messages. That’s especially important for creating event buses so **it’s not covered in this article**, but I want to let you know about it. It could look like:
```yaml
default_middleware: allow_no_handlers
```

### Messenger transports
Okay. So the next lines of given YAML configuration define transports (ways to handle messages). In this example, you can see the `sync` and `async` as the simplest cases. **Sync** means the transport is realized synchronously in the request-response-cycle and **async** is about doing something in the background (in simple words). Let’s stop for a moment here. If you’re testing-freak — **it’s good to add the definition of async for test purposes**. You can do that creating a test configuration like the following one:
```yaml
# /config/packages/test/messenger.yaml

framework:
    messenger:
        transports:
            async: 'in-memory://'
```
Thanks to this — your functional testing is dispatching asynchronous messages in memory (instead of RabbitMQ or something) so you can verify the behavior of the application the closest to real conditions. That’s just a testing-tip — [for more you can visit this article](https://patryk.it/three-simple-testing-tricks-using-php-and-symfony).

### Tag your handlers
So you have configured buses and transports. It's time to tag your handlers to specific buses using Messenger mechanisms. That’s easy. You can add the subsequent lines to your main `services.yaml`.
```yaml
# /config/services.yaml

services:
    _instanceof:
        App\Common\CQRS\CommandHandler:
            tags:
                - { name: messenger.message_handler, bus: command.bus }
        App\Common\CQRS\QueryHandler:
            tags:
                - { name: messenger.message_handler, bus: query.bus }
```
Thanks to this, all the query and command handlers are tagged as Symfony message handlers to the specific buses.

## Solving business use-cases in handlers
Finally, we’re here! Let’s dive into the solving problems using business-case handlers.
In this example, we’re in the e-Commerce context, so we have a use-case like: create a shop if the given shop name is unique. This case could be implemented using simple CRUD mechanisms and scaffolded controllers, but **WE ARE AMBITIOUS**… and that’s just an example.

### Command + Handler
Let’s start by creating a command and his handler. In this case, we’re using UUIDs for identifying the resources so IDs are strings. This process will be asynchronous.
```php
<?php

declare(strict_types=1);

use App\Common\CQRS\Command;

final class CreateShopCommand implements Command
{
    private string $id;
    private string $name;
    
    public function __construct(string $id, string $name)
    {
        $this->id = $id;
        $this->name = $name;
    }
    
    public function id(): string
    {
        return $this->id;
    }
    
    public function name(): string
    {
        return $this->name;
    }
}
```
That’s simple, huh? Now it’s time to create a handler of this command.
```php
<?php

declare(strict_types=1);

use App\Common\CQRS\Command;
use App\Common\CQRS\CommandHandler;

final class CreateShopHandler implements CommandHandler
{
    private ShopFactory $shopFactory;
    private Shops $shops;
    
    public function __construct(CompanyFactory $shopFactory, Shops $shops)
    {
        $this->shopFactory = $shopFactory;
        $this->shops = $shops;
    }
    
    public function __invoke(CreateShopCommand $command): void
    {
        if ($this->shops->existsWithName($command->name())) {
            throw ShopNameTaken::withName($command->name());
        }
        
        $shop = $this->shopFactory->create($command->id(), $command->name());
        
        $this->shops->add($shop);
    }
}
```
Suppose that we already have a shop factory class (`ShopFactory`), and the repository of shops (`Shops`). They aren’t covered in this article. The write-model realized by the Command+Handler is checking if the given shop name is unique and any other shop doesn’t exist with this name. If exists then we’re throwing an exception and discarding creating shop process ([here is can read more about raising exceptions](https://patryk.it/short-history-of-raising-errors)).

### Query + Handler
As you see — we have our write-model done. Let’s consider creating a read-model for use in controllers, validators, forms, etc. Maybe you remember what you have read a few seconds before: we’re throwing an exception when the given shop name is already taken. **We don’t want to face users with this exception moreover we need to do a pre-check before dispatching the command**. Ah, okay — this command is processed asynchronous so the user’s experience would be a false-positive because he wouldn’t see any warning, error, problem — **nothing**! That’s another reason to create a read-model and give the user a satisfying experience with nice information about failed action. Look!
```php
<?php

declare(strict_types=1);

use App\Common\CQRS\Query;

final class ShopExistsWithNameQuery implements Query
{
    private string $name;
    
    public function __construct(string $name)
    {
        $this->name = $name;
    }
    
    public function name(): string
    {
        return $this->name;
    }
}
```
And now look at the query handler. The class name can be strange to you. Prefix DoctrineDBAL means the implementation is provided using Doctrine ORM connection. This class should be placed with the other infrastructural parts of your system in a path similar to `src/Shops/Infrastructure/Doctrine/DBAL/Query/DoctrineDBALShopExistsWithNameHandler`. So long name, and I’m not a Java dev. 😅
```php
<?php

declare(strict_types=1);

use App\Common\CQRS\QueryHandler;

final class DoctrineDBALShopExistsWithNameHandler implements QueryHandler
{
    private Connection $connection;
    
    public function __construct(Connection $connection) // Let's assume that's doctrine connection
    {
        $this->connection = $connection;
    }
    
    public function __invoke(ShopExistsWithNameQuery $query): bool
    {
        /** @var ResultStatement $statement */
        $statement = $this->connection
            ->createQueryBuilder()
            ->select([
                '1',
            ])
            ->from('shop', 's')
            ->where('s.name = :name')
            ->setParameter('name', $query->name())
            ->execute();

        return !empty($statement->fetch(FetchMode::ASSOCIATIVE));
    }
}
```

Another way to implement a query handler is to create an abstraction of the connection. I think that’s an unnecessary level of abstraction in this case and your query-handler will just forward the parameter to it so **it’s an anti-pattern in the terms of modular programming** (shallow method). This way may create god-classes like repositories that are cover n-cases. The first solution (from the above example) has pros like **ONE query** for **ONE implementation** of **ONE business-case query**.

## Yaaay! We have implemented simple CQRS — let’s use it!
Let’s presume that we have a controller that is using our newly created functionalities. We want to create a shop with a specific name. In the first step, we need to do input validation (**name cannot be empty and must be strict**) and then business validation (**shop name is unique**).

Maybe I will just show my simple implementation?
```php
<?php

declare(strict_types=1);

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpFoundation\Request;

final class CreateShopController extends AbstractController
{
    private QueryBus $queryBus;
    private CommandBus $commandBus;
    
    public function __construct(QueryBus $queryBus, CommandBus $commandBus)
    {
        $this->queryBus = $queryBus;
        $this->commandBus = $commandBus;
    }
    
    public function __invoke(Request $request): JsonResponse
    {
        $name = (string) $request->get('name');
        
        if (empty($name)) {
            return new JsonResponsee([
                'error' => 'shop \'name\' can not be empty',
            ], JsonResponse::HTTP_BAD_REQUEST);
        }
        
        if ($this->shopWithNameExists($name)) {
            return new JsonResponse([
                'error' => 'given shop name is already taken',
            ], JsonResponse::HTTP_CONFLICT);
        }
        
        $command = new CreateShop('id', $name);
        $this->commandBus->dispatch($command);
        
        return new JsonResponse([
            'status' => 'Shop creating started!',
            'shop_id' => $command->id(),
        ], JsonResponse::HTTP_ACCEPTED);
    }
    
    private function shopWithNameExists(string $name): bool
    {
        return $this->queryBus->handle(new ShopExistsWithNameQuery($name));
    }
}
```
As you can see I’ve added a private method for the handling query — just for a strictly typed result. The query is done via the synchronous process, and we’re waiting for the response from the read-model, but the write-model is realized via asynchronous processing, so we can’t wait for the result. You can also create a validator using our query to check if the name is unique and create for it specific validation constraint.

## Testing CQRS systems
You may be asking yourself how to test applications based on CQRS architectural approach. It’s not that hard — but of course, exist many ways to do that.

### Unit testing
Using the unit tests you can check the domain-layer that is not covered in this article. I mean — these blocks will be used inside of the application layer (use-cases) which are covered in our example via Command Handlers.

The next thing that you can check via unit tests is an infrastructure layer that is not an adapter of the external world like implementations of the repositories, HTTP clients, etc. In this example, you can do that with the Messenger implementations of the buses. They are based on the interface MessageBusInterface so you can easily mock it using spy test-double to verify if something occurred.
```php
<?php

declare(strict_types=1);

use App\Common\CQRS\Command;
use App\Common\CQRS\MessengerCommandBus;
use App\Tests\Common\CQRS\TestDouble\DummyCommand;
use PHPUnit\Framework\TestCase;
use Symfony\Component\Messenger\Envelope;
use Symfony\Component\Messenger\MessageBusInterface;

final class MessengerCommandBusTest extends TestCase
{
    private MessageBusInterface $symfonyMessageBus;
    private MessengerCommandBus $commandBus;

    protected function setUp(): void
    {
        $this->symfonyMessageBus = $this->assembleSymfonyMessageBus();
        $this->commandBus = new MessengerCommandBus($this->symfonyMessageBus);
    }

    public function testMessageForwardedToMessageBusWhileDispatching(): void
    {
        $command = new DummyCommand();
        $this->commandBus->dispatch($command);

        self::assertSame($command, $this->symfonyMessageBus->lastDispatchedCommand());
    }

    private function assembleSymfonyMessageBus(): MessageBusInterface
    {
        return new class() implements MessageBusInterface {
            private Command $dispatchedCommand;

            public function dispatch($message, array $stamps = []): Envelope
            {
                $this->dispatchedCommand = $message;

                return new Envelope($message);
            }

            public function lastDispatchedCommand(): Command
            {
                return $this->dispatchedCommand;
            }
        };
    }
}
```
In this example I’ve used an anonymous class that is implementing `MessageBusInterface` — but in a real-world application, you can split it as a `SpyMessageBus` with the `lastDispatchedCommand(): Command. Also, the application-layer is unit-testable.

### Integration testing
In a system like this, you should test with integration tests the adapters to the external world. In this example, we don’t have them, but you could check via integration testing things e.g. like the implementation of the Doctrine repository. For info what is a mother object [check this article](https://patryk.it/three-simple-testing-tricks-using-php-and-symfony).
```php
<?php

declare(strict_types=1);

use Doctrine\ORM\EntityManagerInterface;
use Symfony\Bundle\FrameworkBundle\Test\KernelTestCase;

final class DoctrineORMShopsTest extends KernelTestCase
{
    private EntityManagerInterface $em;
    private DoctrineORMShops $repository;

    protected function setUp(): void
    {
        static::bootKernel();

        $this->em = self::$container->get(EntityManagerInterface::class);
        $this->repository = new DoctrineORMShops($this->em);
    }

    public function testShopSucceessfullyAdded(): void
    {
        $shop = ShopMother::any();

        $this->repository->add($shop);

        /** @var Shop $existing */
        $existing = $this->repository->ofId($shop->id());

        self::assertEquals($shop->id(), $existing->id());
    }

    public function testShopNotFoundWhenWasNotAdded(): void
    {
        $this->expectException(ShopNotFound::class);

        $this->repository->ofId(ShopIdMother::any());
    }
}
```

### Functional testing
As functional testing, I mean checking if the whole process works and returns the expected result. These tests are not using third party adapters like Doctrine, etc. — they should use fake implementations that help us with quick functional application testing. We can test controllers, UI CLI commands, and any place that is our user interface layer of the system.
```php
<?php

declare(strict_types=1);

use Symfony\Bundle\FrameworkBundle\KernelBrowser;
use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;
use Symfony\Component\HttpFoundation\Response;

final class CreateShopControllerTest extends WebTestCase
{
    private KernelBrowser $client;

    protected function setUp(): void
    {
        $this->client = self::createClient();
    }

    public function testShopSuccessfullyCreated(): void
    {
        $this->client->request('POST', '/shops', [
            'name' => 'My great shop',
        ]);

        self::assertEquals(Response::HTTP_ACCEPTED, $this->client->getResponse()->getStatusCode());
    }
    
    public function testCannotCreateShopWhenNameTaken(): void
    {
        $existingShop = ShopMother::any();
        static::$container->get(Shops::class)->add(existingShop); // You can replace it with any fixture-lib
    
        $this->client->request('POST', '/shops', [
            'name' => ShopMother::NAME, // We're taking the same name as existing in 30 line
        ]);

        self::assertEquals(Response::HTTP_CONFLICT, $this->client->getResponse()->getStatusCode());
    }
}
```
Of course, you should verify if the response is invalid format, etc. Similarly, we could test the CLI commands.

## So…
As you see implementing CQRS in the Symfony using the Messenger component is pretty easy. At this moment I recommend to you read some articles about the whole approach that CQRS is:
- [DDD, Hexagonal, Onion, Clean, CQRS, … How I put it all together](https://herbertograca.com/2017/11/16/explicit-architecture-01-ddd-hexagonal-onion-clean-cqrs-how-i-put-it-all-together/)
- [All things CQRS](https://github.com/ddd-by-examples/all-things-cqrs)
