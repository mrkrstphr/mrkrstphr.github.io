---
layout: post
title: PostgreSQL arrays and PHP's str_getcsv()
excerpt: PostgreSQL's arrays are sweet, but dealing with them in PHP can be a PITA. I cheat.
categories:
- Database
- Programming
tags:
- PHP
- PostgreSQL
status: publish
type: post
published: true
---

Yesterday, while trying to figure out the best way to deal with PostgreSQL arrays in PHP, I came across the new
`str_getcsv()` function in PHP as of 5.3. This function works much the same as `fgetcsv` to parse a CSV line, except
that it works on a string instead of a file.

For quick reference, CSV looks like this:

```text
"My Name",14,"32,000","2009-04-15"
```

And the return value of a PostgreSQL array looks like this:

```text
{"My Name",14,"32,000","2009-04-15"}
```

Notice the similarities?

We can use PHP's `trim` and `str_getcsv` to turn this PostgreSQL array into a PHP array:

```php
<?php

$data = str_getcsv(trim($record->value, '{}'));
```

Simple as simple does. As long as your array has only a single dimension. If you're using multidimensional arrays in
PostgreSQL then you're dead to me.
