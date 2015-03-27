---
layout: post
title: ! 'PHP''s Magic Quotes: Taking Control of Your Data'
excerpt: Magic quotes can be the bane of any PHP programmer's existence. Let's wrangle them in.
categories:
- Programming
tags:
- PHP
status: publish
type: post
published: true
---

**This post is old and pointless. Magic quotes are dead. PHP 6 was never born. Carry on...**

Magic quotes can be the bane of any PHP programmer's existence. The feature is intended to be helpful and ward off
possible SQL injections, but when you're developing open-source applications, you never know who's going to have it on.
So do you code for magic quotes or not? All politics aside, I'm going to say not. But it is possible to cater to
servers with it both on and off.

In PHP 6, magic quotes will no longer exist, leaving legacy applications that take advantage of it
(and register globals) in the dust. So what do we do to prepare for this big change in PHP functionality? We code like
magic quotes doesn't exist. And just in case it does exist, we reverse it's effects.

There are two major types of magic quotes (and a third, which I won't get into). First is magic_quotes_runtime. When
set to true, this feature escapes single and double quotes, backslashes and NULL values in data read from both files
and databases. This one is easy to get around, since you have control over it before it's used.

```php
<?php

set_magic_quotes_runtime(false);
```

We can tell PHP not to screw with our external data sources. Instead, we'll escape it ourselves using `addslashes()``.

The second type of magic quotes is magic_quotes_gpc, which escapes the same characters as `magic_quotes_runtime`,
except this time in values found in `_GET`, `_POST`, and `_COOKIE`. We have no control over this type of magic quotes,
since by the time any of our code is executed, these superglobals already exist.

In order to undo this, we have to go through the painful process of stripping the quotes ourselves (using
  `stripslashes()`), but we want to make sure we only do this if `magic_quotes_gpc` is enabled, otherwise we might be
  removing valid backslashes.

```php
<?php

if (get_magic_quotes_gpc()) {
  // thanks to http://php.net/magic_quotes:
  function stripslashes_deep($value) {
    $value = is_array($value) ?
      stripslashes_deep($value) : stripslashes($value);

    return $value;
  }

  $_GET = stripslashes_deep($_GET);
  $_POST = stripslashes_deep($_POST);
  $_COOKIE = stripslashes_deep($_COOKIE);
  $_REQUEST = stripslashes_deep($_REQUEST);
}
```

Here, we recursively loop through `_GET`, `_POST`, `_COOKIE` and `_REQUEST` and remove slashes from the data. The
purpose of the recursion is to also clean up the values of any arrays that are stored in these superglobals.

Now there's one more thing we have to do in order to call it good. Since magic quotes will no longer exist in PHP 6,
we're only going to want the above code to run on versions of PHP &lt; 6. Even though (at the time of writing this),
PHP 6 isn't anywhere near released, we'd much rather get our code ready now, rather than worry about it later.

Using the version_compare function, as well as the PHP constant `PHP_VERSION`, we can do a quick check to see what
version of PHP we're running to determine whether or not to execute this chunk of code:

```php
<?php

if (version_compare(PHP_VERSION, "6.0", "&lt;")) {
  // We prefer to clean up our own data (PHP versions &lt; 6):
  set_magic_quotes_runtime(false);

  if (get_magic_quotes_gpc()) {
    // thanks to http://php.net/magic_quotes:
    function stripslashes_deep($value) {
      $value = is_array($value) ?
        stripslashes_deep($value) : stripslashes($value);

      return $value;
    }

    $_GET = stripslashes_deep($_GET);
    $_POST = stripslashes_deep($_POST);
    $_COOKIE = stripslashes_deep($_COOKIE);
    $_REQUEST = stripslashes_deep($_REQUEST);
  }
}
```

And there you have it. It's now safe for us to go ahead and assume that we're going to need to escape our own data,
and we won't have to worry about double-escaping it.
