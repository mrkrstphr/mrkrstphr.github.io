---
layout: post
title: ! "Using Interfaces Effectively in PHP"
excerpt: Interfaces in PHP contain no code; they're just a collection of thoughts. How is this useful?
categories:
- Programming
tags:
- php
- oop
status: publish
type: post
published: true
---

Yesterday, a
[question appeared on Reddit](http://www.reddit.com/r/PHP/comments/30904p/i_dont_understand_the_usefulness_of_interface/)
about the purpose of interfaces in PHP. While I was too late to the party to provide an answer to
that thread (at least that would get noticed by anybody), I thought it was a great topic of
conversation.

So let's take a look at interfaces in PHP.

## Interfaces

Interfaces are a lot like classes, except with much less features. In fact, there are only two
things you can do in an interface:

 1. Define public method stubs, or public methods signatures without bodies, and
 2. Extend another interface

This ends up looking something like:

{% highlight php startinline %}
interface QueryInterface {
  public function whereId($id);
}

interface CustomerQueryInterface extends QueryInterface {
  public function whereType($type);
}
{% endhighlight %}

Following the normal rules of inheritance, the `CustomerQueryInterface` defines two methods:

 1. `whereId($id)` from the parent `QueryInterface`
 2. `whereType($type)` from itself

Again, these methods cannot have bodies in an interface. So where is the usefulness in this?

Note: you don't have to name an interface with the suffix of `Interface`. Here, I'm just following
the [handy naming guidelines](https://github.com/php-fig/fig-standards/blob/master/bylaws/002-psr-naming-conventions.md)
used internally by the [PHP Framework Interop Group (PHP-FIG)](http://www.php-fig.org/). You
can name them however your heart desires.

### Extending Interfaces

When a normal class implements an interface, it **must** implement all methods defined in that
interface. In this sense, it's sometimes described as a contract as, in order to use the interface,
the extending class must follow the contract of that interface.

Of course, an implementing class can implement other methods outside of the interface, but it must
at least implement all of those methods.

So what if we wanted to use the `CustomerQueryInterface`?

{% highlight php startinline %}
class CustomerQuery implements CustomerQueryInterface {
  public function whereId($id) {
    // do something to get Customer by $id
  }

  public function whereType($type) {
    // do something to get Customer by $type
  }
}
{% endhighlight %}

We use an interface by using the `implements` keyword. It's very similar to extending other classes,
but only works for interfaces. The `implements` keyword also allows us to implement multiple
interfaces, whereas extending in PHP only allows you to extend one class.

The `CustomerQuery` class is required to implement both the `whereId($id)` and `whereType($type)`
methods of the `CustomerQueryInterface`. It must adhere to the contract of the interface.

But again: how is this useful?

## Dependency Injection

To see one of the biggest uses of interfaces, we first must take a detour and discuss the topic of
dependency injection.

Dependency injection removes the management or instantiation of dependencies from the class using
that dependency, the dependent class, and gives it to some external agency, usually the mechanism
responsible for instantiating the dependent class.

Essentially, with dependency injection, or dependencies are given to us, rather than us creating
them.

So if you previously had a class that looked like this:

{% highlight php startinline %}
class CustomersController {
  private $customerQuery;

  public function __construct() {
    $this->customerQuery = new CustomerQuery();
  }
}
{% endhighlight %}

With dependency injection, this class would be refactored to look like:

{% highlight php startinline %}
class CustomersController {
  private $customerQuery;

  public function __construct(CustomerQuery $customerQuery) {
    $this->customerQuery = $customerQuery;
  }
}
{% endhighlight %}

Now, instead of instantiating our dependency, `CustomerQuery`, we're given, or injected with, this
dependency. Obviously this begs the question "how does `CustomerQuery` get instantiated and passed
to the `CustomerController`, then? That's a topic for another time, but obviously whatever is
responsible for instantiating the controller is now responsible for instantiating the query as well.

This is a seemingly tiny change, but it opens up a world of possibilities for our code:

 1. We can now swap out implementations of `CustomerQuery` without changing the code using it
 (almost, we have one more change to make in just a second). And because of this,
 2. We can test the `CustomerController` much easier, and in isolation, by now mocking the
 `CustomerQuery` object instead of instantiating it, and likely turning up a database instance just
 to test our controller.
 3. Our code is now more expressive. It's easier to tell that it depends on `CustomerQuery` because
 we had to pass it when instantiating it. Previously, without digging into the code, we had no idea
 what the dependencies of this class might be.

So for the third time: how does this make interfaces useful?

## Interface Type Hinting

In our previous example of dependency injection, we used a type hint in the constructor for
`CustomerQuery`. Remember, `CustomerQuery` is a concrete implementation of the
`CustomerQueryInterface` interface.

We can take this refactoring one step farther and, instead of type hinting the concrete
implementation, we can instead type hint to the `CustomterQueryInterface`:

{% highlight php startinline %}
class CustomersController {
  private $customerQuery;

  public function __construct(CustomerQueryInterface $customerQuery) {
    $this->customerQuery = $customerQuery;
  }
}
{% endhighlight %}

This where the concept of adhering to a contract really comes into play: our `CustomersController`
declares that it requires an instance of `CustomerQueryInterface`. It doesn't care what concrete
instance of this interface it receives, just that it receives something with the methods it expects.

This allows us to decouple our controller from an actual implementation, and just couple it to a
specification. Further, it allows us to swap out the concrete implementation with another, without
having to change anything with the code using it.

This makes interfaces exceptionally useful and powerful in PHP.

## Interfaces are Contracts

Simply put: interfaces are contracts that some code implements and adheres to and other code
depends upon. It allows us to have assurance that any given dependency will implement the methods
we expect. Even further, with the upcoming PHP 7, we can also use type hints (object and scalars)
and return types to further ensure that concrete implementations adhere to the contracts.

Checkout my article on
[Implementing the Onion Architecture in PHP](http://localhost:4000/2013/07/04/implementing-the-onion-architecture-in-php/)
for more examples of how to use interfaces to achieve cleanly architected, decoupled code.
