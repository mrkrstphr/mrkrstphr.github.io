---
layout: post
title: ! 'Testing PHP Apps with Peridot'
excerpt: PHPUnit is the defacto king of PHP testing, but Peridot is a refreshing kick in the face.
categories:
- Programming
- Testing
tags:
- php
- deployment
- behavior driven development
- bdd
- testing
status: publish
type: post
published: true
---

[Pretty](https://twitter.com/kentbeck) [much](https://twitter.com/martinfowler) [everyone](https://twitter.com/dhh)
[agrees](https://twitter.com/grmpyprogrammer) that you should be testing your applications, libraries, frameworks, etc,
in some way or another.

Sure, there are valid times not to test, like when working on a proof of concept or with an unknown market, but those
times are pretty few and far between. And if that app actually takes off, you should either rewrite it from scratch with
tests, or get tests written for it ASAP.

In PHP Land, [PHPUnit](https://phpunit.de/) has pretty much dominated the scene. It's been around forever, and nearly
every major project uses it.

I've been using PHPUnit for awhile now, simply because that's what PHP projects use. I've never given it much thought,
even though at times, it's been exceedingly frustrating to use, especially when dealing with
[mock objects](http://c2.com/cgi/wiki?MockObject).

On the Behavior-Driven Development (BDD) front, the big name is [Behat](http://behat.org), and other players, like
[phpspec](http://phpspec.net) have existed for awhile.

I've used Behat a bit, but it always felt alien and awkward to me. It just didn't feel right. And while I haven't
tried it, phpspec looks much the same: just awkward.

Enter Peridot.

## Peridot

[Peridot](http://peridot-php.github.io) is a spec-based, BDD testing framework. Writing tests has never felt so
freeing. It just feels right.

If you've ever used [Jasmine](http://jasmine.github.io/) for JavaScript testing or [RSpec](http://rspec.info/) for
Ruby testing, Peridot will feel very familiar.

## Peridot's Approach

Specs in Peridot look something like this:

```php
<?php

describe('Maths', function () {
  describe('addition', function () {
    context('when the numbers are negative', function () {
      it('should generate a negative sum', function () {
        expect(-5 + -5)->to->equal(-10);
      });
    });

    context('when the numbers are positive', function () {
      it('should generate a positive sum', function () {
        expect(5 + 5)->to->equal(10);
      });
    });
  });
});
```

These `describe` and `context` blocks can nest as deep as you need them to go. This ends up giving you some very
helpful output when running your specs:

    $ ./vendor/bin/peridot foo.spec.php

      Maths
        addition
          when the numbers are negative
            ✓ should generate a negative sum
          when the numbers are positive
            ✓ should generate a positive sum


      2 passing (3 ms)

The output is even gorgeous and expressive. But if you're missing the simple dots from PHPUnit, you can drop in a
different [output reporter](http://peridot-php.github.io/plugins.html#reporters).

## Next Level Assertions with Leo

The Peridot project also supplies an assert library, called [Leo](http://peridot-php.github.io/leo/). I've used this
in the examples above:

```php
<?php

expect(5 + 5)->to->equal(10);
```

Leo also comes with an assert library, if you prefer that format of testing:

```php
<?php

$assert = new Assert();
$assert->typeOf('string', 'hello', 'is string');
$assert->operator(5, '<', 6, 'should be less than');
$assert->isResource(tempfile());
$assert->throws(function() {
    throw new Exception('exception');
}, 'Exception');
```

Leo supports a pretty expansive matcher library, with a plugin system for adding additional matchers.

Checkout the [HTTP Foundation plugin](https://github.com/peridot-php/leo-http-foundation) for Leo, which makes testing
Symfony-based applications incredibly easy:

```php
<?php

expect($response)->to->allow(['POST', 'GET']);
expect($response)->to->have->status(200);
expect($response)->json->to->have->property('name');
```

## Concurrency

One of the biggest wins for using Peridot is the
[concurrency plugin](https://github.com/peridot-php/peridot-concurrency), which allows you to run your slow tests
concurrently to get the results faster.

This is great if you have a large, functional test suite that takes forever to run. It's quite literally a game
changer.

## Plugins

Peridot is awesome because of its [event based architecture](http://peridot-php.github.io/events.html) and its awesome
[plugin system](http://peridot-php.github.io/plugins.html).

If Peridot doesn't do something you want it to do, you can simply write a plugin to extend the functionality.

My favorite plugin is the [watcher plugin](https://github.com/peridot-php/peridot-watcher-plugin), which automatically
re-runs your tests whenever the code changes. Now I know immediately when I've screwed something up!

## More Information

If you want to learn more information about Peridot, checkout [their website](http://peridot-php.github.io/). You can
also checkout the slides on talks the developers gave at [GrPhpDev](http://meetup.com/GrPhpDev):

 * [Peridot in Action](http://peridot-php.github.io/peridot-in-action/)
 * [Extending Peridot](http://austinsmorris.github.io/extending-peridot)

## Full Disclosure

For the sake of full disclosure, I should probably point out that I work with the two developers who wrote Peridot.
That doesn't change how epically awesome it is though. Check it out!
