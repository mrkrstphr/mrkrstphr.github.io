---
layout: post
title: Constraints in Rails Migrations
excerpt: Building constraints in Rails applications.
categories:
- Programming
tags:
- Ruby on Rails
status: publish
type: post
published: true
---

I decided to try a rails project again... something I do quite frequently after I've completely forgotten everything
I've learned.

One thing I quickly remembered is that there is no easy way in a migration to create constraints, like a foreign key.
You have to do some exec silliness with an actual query.

After some quick searching, I found this plugin: [mig-constraints](http://rubyforge.org/projects/mig-constraints).

It gives you the ability to do this to specify some common constraints in the migration.

**References**

```ruby
table.column :user_id, :integer, :references => :users
```

**Unique**

```ruby
table.column :username, :string, :unique => true
```

**Check**

```ruby
table.column :unit_price, :double, :check => '> 0'
```

Very helpful. Sadly, like all Rails plugins, needs to be installed per project.
