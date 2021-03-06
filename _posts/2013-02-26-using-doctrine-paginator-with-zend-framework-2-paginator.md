---
layout: post
title: Using Doctrine Paginator with Zend Framework 2 Paginator
excerpt: Doctrine and ZF2 both have a Paginator for pagination of things. Wrapping Doctrine's with a ZF adapter makes
    them play together quite nicely.
categories:
- Programming
tags:
- doctrine
- pagination
- PHP
- zend framework 2
- zf2
status: publish
type: post
published: true
---

So Doctrine has a Paginator that uses DQL and Zend Framework has a Paginator for paginating whatever the eff. So
which do you use, what do you do? Answer: smash them together!

I created a super simple Adapter for `Zend\Paginator` that uses the `Doctrine\ORM\Tools\Pagination\Paginator` class:

```php
<?php

namespace Application\Paginator;

use Zend\Paginator\Adapter\AdapterInterface;
use Doctrine\ORM\Tools\Pagination\Paginator;

class DoctrinePaginatorAdapter implements AdapterInterface {
  protected $paginator = null;
  protected $count = null;

  public function __construct(Paginator $paginator) {
    $this->paginator = $paginator;
    $this->count = count($paginator);
  }

  public function getItems($offset, $itemCountPerPage) {
    return $this->paginator->getIterator();
  }

  public function count() {
    return $this->count;
  }
}
```

Now I have a method in my Repository that gives me a `Doctrine\ORM\Tools\Pagination\Paginator`:

```php
<?php

public function getPaginator($offset = 0, $limit = 20) {
  $dql = 'SELECT e FROM Application\\Domain\\Model\\Entity e ' .
    'ORDER BY e.column ASC';

  $query = $this->manager->createQuery($dql)
    ->setMaxResults($limit)
    ->setFirstResult($offset);

  $paginator = new Paginator($query);

  return $paginator;
}
```

Finally, in my controller, I call this method, pass it off to my `DoctrinePaginatorAdapter`, then hand that off
to `Zend\Paginator`. Boom!

```php
<?php

public function indexAction() {
  $page = $this->params('page');
  $max = 20;

  $results = $this->repository->getPaginator(($page - 1) * 20, $max);

  $adapter = new DoctrinePaginatorAdapter($results);
  $paginator = new Paginator($adapter);
  $paginator->setCurrentPageNumber($page);
  $paginator->setItemCountPerPage($max);

  return array(
      'paginator' => $paginator
  );
}
```

And just like that, I have pagination marrying Zend Framework and Doctrine's pagination wizardry. So simple,
even a caveman could do it!<sup>TM</sup>
