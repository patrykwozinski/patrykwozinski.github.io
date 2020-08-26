---
layout: post
title: Three simple testing tricks using PHP and Symfony
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [testing, symfony, php]
comments: true
---

# Three simple testing tricks using PHP and Symfony
Hi! I’ve written this post to help you and keep in history something that makes application testing easier. The first two tricks are related to the **PHP Symfony** web framework and functional/integration testing in this framework, but the second one is a pattern that every PHP engineer should know. You can treat this article as a note created for me from the past.

### Separated implementations for functional testing and the other environments
While I was working on my side-project I needed something that enables me to use different implementations of some interfaces in the testing environment and the others. I couldn’t find it in the framework guide so maybe it’s helpful not only for me.
In the example, we have an interface like:
```php
interface Customers
{
	/** @throws CustomerNotFound **/
	public function ofId(CustomerId $id): Customer;
}

```

And we have also two implementations. First one is a DoctrineORM implementation:
```php
final class DoctrineORMCustomers implements Customers
{
	public function ofId(CustomerId $id): Customer
	{
		// Blabla using EntityManager stuff
	}
}
```

The second one implementation is just **InMemory** version that we need in the tests (for example units). Yes - that’s just a fake test double implementation. ;)
```php
final class InMemoryCustomers implements Customers
{
	public function ofId(CustomerId $id): Customer
	{
		// Blabla using in memory
	}
}
```

Okay, at this moment we have two implementations. One is using external things (in this example Doctrine and database) and the second one is working in memory so it’s using state of the object.

As you may know or now - functional tests are not using third party adapters. They should just check the flow and the final result of a request. For example - check the status code of a response.

Coming to the point - we want to use it in different situations - other implementations. As a default, we’re registering services in something like `/config/services.yaml`. But how to change implementation for the tests? I’ve moved definitions to the `/config/packages/services.yaml` and created `/config/packages/test/services.yaml` with the same keys but different implementation classes. That’s easy and allows us to make the functional tests as fast as we can do them.
Definitions should looks like:
```yaml /config/packages/services.yaml
services:
	App\Domain\Customers: '@App\Infrastructure\Doctrine\ORM\DoctrineORMCustomers'
```

```yaml /config/packages/test/services.yaml
services:
	App\Domain\Customers: '@App\Infrastructure\InMemory\InMemoryCustomers'
```

Thanks to this you can make some fast functional tests of your application. Remember: tests must be quick because slow tests destroying your developer experience.

### Testing dependency injection container is close to you, seriously
This information is not that hard to find but you can use your testing container (that marks all the services as a public) using a magic static test attribute `static::$container`.
Thanks to this you can easier test some services that are registered as a private for the container. AND! Remember to mark them as **final/private**.

### ObjectMother is your friend
This tip is not related to the Symfony framework but still, it’s really useful. ObjectMother is a great testing pattern that could avoid code duplication and simplify tests. Sometimes your testing code - especially **given** section may look not that cool as you want it to be. The situation could look like the following example:
```php
public function testPremiumCustomerDidSomethingBlabla(): void
{
	// Given
	$email = new Email('patryk.wozinski@example.com', 'verified');
	$address = new Address('Warsaw', 'Ursynów');
	$customer = new Customer($email, $address);

	// When
	// Blurred :)
}
```

Let’s suppose that PREMIUM CUSTOMER is just a Customer with a verified e-mail address. Now image that - you need the same state of the customer in… hmm, seven test cases. It’s boring and painful to copy&paste code pieces. And… after your change, the `$address` needs the third parameter so you need to change all the seven test cases. That’s terrifying!
To avoid such sad situations you can introduce **ObjectMother** pattern in your test code. You can create few objects and then compose them into some detailed object.
Let’s look at the bigger example:
```php
final class EmailMother
{
	private const EMAIL = 'patryk.wozinski@example.com';
	private const STATUS_VERIFIED = 'verified'; // I hate "statuses" but that's just an example xd

	public static function anyVerified(): Email
	{
		return new Email(self::EMAIL, self::STATUS_VERIFIED);
	}
}

final class AddressMother
{
	private const CITY = 'Warsaw';
	private const DISTRICT = 'Ursynów';

	public static function any(): Address
	{
		return new Address(self::CITY, self::DISTRICT);
	}
}

// And finally the most interesting for us
final class CustomerMother
{
	public static function anyPremium(): Customer
	{
		return new Customer(EmailMother::anyVerified(), AddressMother::any());
	}
}
```

So… right now your testing method could looks like:
```php
public function testPremiumCustomerDidSomethingBlabla(): void
{
	// Given
	$customer = CustomerMother::anyPremium();

	// When
	// Blurred :)
}
```
At this moment any change of the customer’s interiors doesn’t affect your tests. It’s much CHEAPER and EASIER to maintain. Two wins with just one simple pattern.

Happy coding! 👋
