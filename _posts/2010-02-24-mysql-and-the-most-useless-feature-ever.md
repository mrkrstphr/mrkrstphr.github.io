---
layout: post
title: MySQL and the Most Useless Feature Ever
excerpt: Just what the hell is the point of REFERENCES in MySQL?
categories:
- Database
tags:
- MySQL
status: publish
type: post
published: true
---

While building MySQL database support into the OpenAvanti PHP framework, I came across an interesting quirk in MySQL
that I thought must be a bug. Apparently, after submitting a bug report and getting a response from MySQL, it's a
documented feature (and later, referred to as a limitation).

The following create table statements are valid in MySQL:

```sql
CREATE TABLE users(
    user_id int PRIMARY KEY,
    account_id int REFERENCES accounts(account_id)
) ENGINE=INNODB;

CREATE TABLE users(
    user_id int PRIMARY KEY,
    account_id int,
    FOREIGN KEY(account_id) REFERENCES accounts(account_id)
);
```

Aft first glance, one would assume that both of these tables have some sort of reference between `users.account_id`
and `accounts.account_id`, but apparently assuming makes an ass out of one's self. When using the `REFERENCES` syntax
on a column, no foreign key relationship is created, nor is any kind of reference created. In fact, MySQL does
absolutely nothing with this clause except validate it syntactically.

I reported a bug on this, assuming that this column reference clause is supposed to have some sort of affect, but was
quickly pointed to the [MySQL documentation](http://dev.mysql.com/doc/refman/5.0/en/example-foreign-keys.html) where
it points out that this handy dandy little feature does absolutely nothing. In fact, according to the documentation,
you can also do an `ON DELETE` or `ON UPDATE` command which is also ignored! Fantastic, eh?

According to the documentation, this clause exists merely as
> a memo or comment to you that the column which you are currently defining is intended to refer to a column
> in another table.

A memo or comment you say? But pray tell how something can be useful if it simply disappears?

Seriously, it simply disappears. Do a dump on the database or do `SHOW CREATE TABLE tablename`. Your handy memo is
gone! Just like any other SQL comment would be gone!

So this begs the question, why not just use an a comment? Did the MySQL developers seriously take the time to add this
syntax to the database engine, write code for it to be parsed and validated, simply for it to be ignored and completely
disappear as soon as you run the `CREATE TABLE` command?

No. I doubt it. I think this is a blatant bug that the organization is just too lazy to fix and are simply labeling a
feature (although later in the bug thread, they start referring to it as a limitation). But why not fix it? It can't be
that hard as I'm sure you would just extend the functionality for table level reference defines, and it's likely not
going to break backwards compatability because A) I doubt anyone is using such a useless feature and B) it doesn't
matter if they are, because as soon as they generated their tables, the information was lost.

So I present to you the most useless feature ever. See the bug report and exchange
[here](http://bugs.mysql.com/bug.php?id=51174).
