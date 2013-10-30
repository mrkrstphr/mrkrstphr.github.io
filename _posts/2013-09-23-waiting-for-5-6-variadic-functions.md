---
layout: post
title: ! 'Waiting for 5.6: Variadic Functions'
excerpt: PHP 5.6 will introduce variadic functions.
categories:
- Programming
- Waiting For
tags:
- '5.6'
- features
- functions
- PHP
- variadic
status: publish
type: post
published: true
---
An [RFC for Variadic Functions](https://wiki.php.net/rfc/variadics) has been
[voted on and accepted](https://wiki.php.net/rfc/variadics#vote) by PHP developers, and should make it into the next
release (5.6).

Variadic functions are ones that accept a variable, or infinite, amount of arguments. There are already internal
functions in PHP that support this (as C does), such as `sprintf` where you can pass it any number of
arguments.

{% highlight php startinline %}
sprintf($format, $arg1, $arg2, $arg3);
{% endhighlight %}

Currently, if you wanted to support this in a user-land PHP function or method, you'd have to use the `func_get_args()`
family of functions to determine what was passed beyond the definition. Now, with this accepted RFC, you can define a
function like follows (stolen straight from the RFC):

{% highlight php startinline %}
public function query($query, ...$params) {
    $stmt = $this->pdo->prepare($query);
    $stmt->execute($params);
    return $stmt;
}
{% endhighlight %}

`$params` will be an array of every additional argument passed to that method.

Checkout the RFC for more examples and details.

<small>* The idea for the "Waiting for..." posts graciously stolen from [depesz](http://www.depesz.com).
Checkout his fantastic PostgreSQL blog.</small>
