---
layout: post
title: array_to_string in PostgreSQL
excerpt: Want to get more work done in a single query? Tired of looping through query results to simply build a list of data?
categories:
- Database
tags:
- PostgreSQL
status: publish
type: post
published: true
---
*This snippet courtesy of [Kieran Smith](http://www.kieransmith.net")*

Want to get more work done in a single query? Tired of looping through query results to simply build a list of data?
How about this?

```sql
SELECT
array_to_string(
    ARRAY(
        SELECT name
        FROM projects
        WHERE customer_id = 10
        ORDER BY LOWER(name)
    ), ', '
) as projects;
```

What you are doing is building an array from the subquery and then turning it back into a string immediately, so you
can display multiple results in a single field. Our results might look something like this:

> gnucashweb, Hangar, OpenAvanti, tarmac

Nice!
