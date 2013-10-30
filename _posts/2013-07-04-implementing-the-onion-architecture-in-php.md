---
layout: post
title: Implementing the Onion Architecture in PHP
excerpt: The Onion Architecture allows for a clean, polished separation of application concerns and makes for a clean,
    refactorable, testable code base.
categories:
- Development
- Programming
tags:
- architecture
- design patterns
- onion
- PHP
status: publish
type: post
published: true
---
Recently, I was turned on to a new design architecture by
[a fellow programmer](https://twitter.com/scaturr), which I've quickly grown to love and adopt. It's called the
**Onion Architecture** and its main principal is this: organize the layers of your application code like the layers
of an onion. Every layer of the onion is only coupled (or dependent upon) any layer deeper in the onion than itself.

At the center of the onion is your domain - your business logic core on which everything in the application depends.
The infrastructure layer (which ties your domain to some sort of data storage) and UI layer are kept at the outermost
layer, as they change most often and, through some methods I'll describe below, actually depend on a lot less of the
application than you would think.

Jeffrey Palermo, who coined the term, probably
[explains it much better in his series of articles](http://jeffreypalermo.com/blog/the-onion-architecture-part-1/).

The benefit of designing in this manner is that any layer of the application can be completely removed and rewritten,
and the only other layers that would need to know about the change are those deeper within the onion. You could fully
swap out which ORM solution you are using and not have to touch your domain, services or UI layer whatsoever.
Likewise, you could swap out your application framework (think Zend Framework, Symfony or CakePHP) and only rewrite
that layer. The possibilities are pretty amazing.

There are several principals to adhere to that make this approach possible. 

### Build a Pure PHP Domain Model

The first step is to build the core of your onion: your domain layer. This is all of your business logic, and it
should be implemented as pure PHP objects, with no dependencies whatsoever. This is straight PHP, that you can move
around and do with whatever you please, swapping out all other technologies and frameworks around it, and still have
your robustly defined PHP business logic intact.

This is the bread and butter of your application; the heart and soul of what it does. Does it manage customer orders?
Then you should probably have a few classes named just that, which can intelligently interact with one another:

{% highlight php %}<?php

/**
 * Class Customer
 */
class Customer extends AbstractEntity
{
    /**
     * @var string
     */
    protected $name;
    
    /**
     * @var array
     */
    protected $orders;

    /**
     * @param string $name
     */
    public function setName($name)
    {
        $this->name = $name;
    }

    /**
     * @return string
     */
    public function getName()
    {
        return $this->name;
    }

    /**
     * @param array $orders
     */
    public function setOrders(array $orders)
    {
        $this->orders = $orders;
    }

    /**
     * @param Order $order
     */
    public function addOrder(Order $order)
    {
        $this->orders[] = $order;
    }
    
    /**
     * @return array
     */
    public function getOrders()
    {
        return $this->orders;
    }
}
{% endhighlight %}

{% highlight php %}<?php

/**
 * Class Order
 */
class Order extends AbstractEntity
{
    /**
     * @var Customer
     */
    protected $customer;

    /**
     * @param \Customer $customer
     */
    public function setCustomer($customer)
    {
        $this->customer = $customer;
    }

    /**
     * @return \Customer
     */
    public function getCustomer()
    {
        return $this->customer;
    }
}
{% endhighlight %}

This pure implementation allows use to work with our business logic using straight PHP objects and completely
decouples it from anything, but, well, PHP. And if you switch that out, then it sounds like you were planning on
rewriting everything anyway.

{% highlight php %}<?php

$customer = new Customer();
$customer->setName('ACME Corp');
$customer->addOrder(new Order());
{% endhighlight %}

Also, if you're not already, you should really think about following the principles of
[Domain-Driven Design](http://www.amazon.com/gp/product/0321125215/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=0321125215&linkCode=as2&tag=kristwilso-20).

### Use an Object Relationship Mapper (ORM)

This isn't necessarily required to implement the Onion Architecture with PHP, but it sure makes a lot of sense. 

We need to interact with the database someway, so we have a few options: 

 - Write straight dirty queries using PDO (if you're using anything lower level than PDO, shame on you).</li>
 - Use an ActiveRecord style design pattern</li>
 - Use an ORM</li>
 - Something else that I don't know about and is probably super trendy.

If your preferred method of operation is #1, then you're creating more work for yourself. Using the Data Mapper or
ActiveRecord pattern is really going to save you a lot of time in the long run, even if it seems like a lot of
learning and configuration ahead of time. The ability to simply retrieve a database row by id, manipulate the
returned object, then save it, either using a method on the object or through a repository method is nice, quick,
clean programming. And the ability to work with objects feels right at home for developers.

If you think ActiveRecord is a good fit, then you really don't understand the goals of this architecture. Using an
ActiveRecord style pattern tightly couples your infrastructure (DBAL) to your domain layer. In fact, the
infrastructure IS your domain layer; they're one in the same in an ActiveRecord implementation. When you decide to
ditch that ActiveRecord library, your whole domain layer needs to be refactored.

The Data Mapper pattern really is the best way to go. It allows you to have your pure PHP domain layer completely
decoupled, and, instead, uses some sort of configuration files (whether they be XML, Yaml, or something else) to map
these objects and their properties to corresponding database tables and and columns.

There is valid criticism against using an ORM: it requires a lot of work setting up your domain objects and creating
your mapping files. But I feel it's worth the hassle (and quickly becomes second nature) in that it allows you a very
strong decoupling of your logic and database.

This method provides the ability to easily swap out ORM libraries, and even data stores -- imagine switching to an
API service layer rather than a direct connection to the database and ONLY having to change around your infrastructure
layer. Your services, controllers, views, etc don't even notice anything changed. That, right there, is power.

### Develop Against Interfaces

The next big helpful thing to do in your code is to develop against an interface, rather than an implementation,
especially when the objects you are using come from a different layer of the application.

This means that when you want to use a class from another component (or layer within your onion), instead of
referencing that component directly, reference an interface which defines its behavior. For instance, let's say
we're using Doctrine and have a custom repository for our Orders (see our example above). In our domain, we'd have
an interface that defines this repository:

{% highlight php %}<?php

namespace Project\Domain\Order;

interface OrderRepositoryInterface extends RepositoryInterface
{
    public function getActiveOrders();
    public function getOrdersByCustomer($customerId);
    public function getShippedOrders();
}
{% endhighlight %}

We define this interface that has the blueprint of the behavior of the actual repository, but as it's an interface,
it lacks the details. Later, within a persistence layer, we'll actually define a repository that implements this
interface:

{% highlight php %}<?php

namespace Project\Persistence\Order;

use Project\Domain\Order\OrderRepositoryInterface;

class OrderRepository implements OrderRepositoryInterface extends AbstractRepository
{
    public function getActiveOrders() { }
    public function getOrdersByCustomer($customerId) { }
    public function getShippedOrders() { }
}
{% endhighlight %}

In our controller, which uses the repository to retrieve requested data, but is part of it's own layer, separate
from both domain and persistence, we only bind the required resource using the interface, and leave it entirely up
to the code responsible for creating the controller to pass in the correct implementation
(see *Use Inversion of Control*) below.

{% highlight php %}<?php

namespace Project\Orders;

use Zend\Mvc\Controller\AbstractActionController;
use Project\Domain\Order\OrderRepositoryInterface;

class OrderController extends AbstractActionController
{
    protected $repository;

    public function __construct(OrderRepositoryInterface $repo) 
    {
        $this->repository = $repo;
    }
}
{% endhighlight %}

Notice that our front-end UI code depends only on our domain layer, and not the persistence layer directly. The code
responsible for creating the controller will pass in the correct `OrderRepository` from the persistence layer, such
that only that code has to be touched if the persistence layer is replaced by a different component.

How do we get the correct repository into the OrderController::__construct()or?

### Use Inversion of Control

Inversion of Control is a programming method in which coupling (the reliance on other modules or libraries within the
application) is bound at run time, rather than through explicit dependencies in the code. We just provided an example
above, in which we define a reliance on an interface, and use some other external means to provide an implementation
of that interface. This is known as **dependency injection**, and it's the route I would recommend for intermixing or
requiring portions of another layer of your onion.

Dependency injection in Zend Framework 2 is handled through controller factories. In the controller config section
of a module, you define a factor to provide the implementation of whatever controller is requested
(like our `OrdersController` above). Through this factory method, you would instantiate a controller object, passing
the `OrderRepository` from the Persistence library as an argument to the constructor.

But where does this factory know where to get the repository? And if it's explicit in that factory, aren't we just
pushing the buck in terms of where we hard code this dependency? Yes, that's one way of doing it. Another way is to
use Zend's Service Manager and the **Service Locator** pattern to go figure out what an `OrderRepository` should be.

The Service Locator is a registry that is used to find requested resources. It alone will know how to fulfill an
`OrderRepository` and, thus, will be the one place we have to change if we ever replace our Persistence layer with
something else (like, MySQL to MongoDB, etc).

In Zend 2, the Service Manager has a config where you will define a factory for `OrderRepository` that will
instantiate it and return it. It's that simple.

Other frameworks (Symfony, Code Igniter, etc) have implementations for Inversion of Control, but I don't know enough
about them to speak to that. A quick internet search will probably easily find the answer.

### Conclusion

By separating out the layers of an application like described above, we're afforded a very low level of coupling,
which will allow us to break away and replace or rewrite pieces or change major pieces of technology
(like going from MySQL to MongoDB or Zend Framework 2 to Symfony) and only have to rewrite a minimal amount of code.
It allows us to be better programmers and prepare for inevitable future change to the project.
