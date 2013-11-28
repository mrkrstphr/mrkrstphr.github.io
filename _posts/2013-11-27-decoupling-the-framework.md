---
layout: post
title: ! 'Decoupling the Framework'
excerpt: We care a lot about decoupling in development, and spend a lot of time trying to get it right. But are we strongly coupling ourselves to our frameworks?
categories:
- Programming
tags:
- methodology
- PHP
status: publish
type: post
published: true
---
We spend a lot of time discussing and analyzing the features and merits of several frameworks, trying very hard to make
sure we find the perfect one to use for our project. Rightfully so: picking the wrong framework can lead to a slew of
issues down the road in terms of maintenance and scalability. But what if it didn't have to? What if the effect of
picking the wrong framework could be negligible? What if it would only rewrite rewriting a small portion of the
application?

We also spent a considerable amount of effort making sure that there is minimal amount of coupling within our code.
Strong coupling leads to problems testing, adapting, refactoring and reusing code. What if we applied that same
principal to dealing with whatever framework we're using?

### The Framework Isn't a Part of Your Application

When writing medium to large applications, it's important to know that the pieces of the application that are
implemented using your framework *aren't* your application; or at least they shouldn't be. Any collection of
controllers, forms, helpers, database abstraction, is not your application. The business logic -- the domain -- is.
The collection of services, repositories, factories and entities *are* your application. The rest -- the stuff the
framework provides -- is just a gateway or GUI sitting on top of your real application.

Use of these framework components should be minimal and abstracted away when possible. Here are a few guidelines I
use to try to achieve this decoupling:

### Controllers

Keep your controllers small and concise. Give them no logic, instead, reserve that for a domain layer of your
application. Controllers should simply interpret the request, collect any provided data, and pass off the processing
to some service, repository, and/or factory. Actions within the controller should contain very few lines; if they
don't, you're doing it wrong.

MVC isn't enough. Put a huge emphasis on M and V, and keep C as light as possible.

### Models

Your models should simply be a representation of your data. They should not contain any logic or knowledge
*whatsoever* of where the data comes from, how to get it or how to send it back. Your data could come from XML files,
a relation database or an API, and your models shouldn't look any different.

Why?

If your models are tightly coupled to some data abstraction or ORM layer (read: an active record pattern), then it's
going to be painful to later rip them out and refactor them all to fight some other DBAL/ORM flavor of the day. Instead,
your models should be plain old PHP objects that are then mapped to whatever data service hydrates them (read: a data
mapper pattern).

Models should be fat on business logic only; not fat on technical implementation logic. A good model looks something
like this:

{% highlight php startinline %}
class Customer
{
    protected $name;

    public function getName() {
        return $this->name;
    }

    public function setName($name) {
        $this->name = $name;
        return $this;
    }
}
{% endhighlight %}

This `Customer` object has absolutely no idea how the hell it gets its data, and it likes it that way. And when you
decide your ditching your ORM for something else, you'll like it too.

### ORMs

Speaking of an ORM, not only should your model not know you're using one, but neither should just about anything
else. The only thing that should know that you're using, say, Doctrine ORM, is the area of the code that **dependency
injects** an instance of something Doctrine aware into the rest of your code.

This means your services, controllers, etc should have no idea you're using doctrine. All they should be aware of is
an interface that they require, and whatever you give them, as long as it conforms to that interface, should be
peaches and gravy.

### So how the hell do you use something like Doctrine?

Doctrine comes with a spiffy `EntityManager` that works as a **unit of work**. This is something your code should
know nothing about.

Do you have a controller that needs some sort of customer repository? Great! That's what it should ask for:

{% highlight php startinline %}
class CustomerController extends AbstractActionController
{
    protected $customerRepository;

    public function __construct(CustomerRepositoryInterface $customerRepo) {
        $this->customerRepository = $customerRepo;
    }

    public function viewAction() {
        $id = $this->params('id');
        $customer = $this->customerRepository->getById($id);
    }
}
{% endhighlight %}

Look at that; seriously, look at that code! It doesn't know you're using Doctrine. It doesn't give a shit what you're
using, as long as you give it something that conforms to `CustomerRepositoryInterface`. So what does *that* look like?

{% highlight php startinline %}
interface CustomerRepositoryInterface
{
    public function getById($id);
}
{% endhighlight %}

Imagine that; this repository interface doesn't know we're using Doctrine either? So what does? Well, eventually
we'll have to have a real repository:

{% highlight php startinline %}
class CustomerRepository
{
    protected $entityType = 'Customer';
    protected $entityManager;

    public function __construct(EntityManager $em)
    {
        $this->entityManager = $em;
    }

    public function getById($id)
    {
        return $this->entityManager->find($this->entityType, $id);
    }
}
{% endhighlight %}

*THIS* class knows about Doctrine, and only this class. So how do you get that in the controller? Some method of
**Inversion of Control**, I would suspect. Dependency injection, maybe a service locator to build that dependency.
That's probably up to your framework. Your framework can *connect* the components, but it shouldn't fully *depend* on
the components.

So now that we have this implementation and we want to switch to using an API, what do we do? We write a new
repository that uses an API implementation and inject that instead. We don't have to touch the controller or any other
services that use this repository.

It's fucking magic. And it leads to having to rewrite a lot less code.

### But those interfaces, that's a lot of code?

No it's not; quit bitching. They're like 40 lines, tops, with no method bodies. How hard is that? Compare it to the
flexibility it gives you and the low amount of code you'd have to refactor making a major change, and it'd take an
idiot not to understand why this is important.

### Decouple Your Framework

Your framework should facilitate your dependencies and bootstrap your actual domain application to achieve your goals.
Your framework should not be extended to create your application; your application should not be tightly coupled to
your framework.

It's like getting in a relationship just because it seems like it might be fun for awhile. You know you probably won't
be there long and something awesome might come along in the future; so why would you move all your stuff in and open
some joint banking accounts? That's welcoming a world of hell later. Don't be that guy.
