---
layout: post
title: "Rails 4: Easily Add Reference to Migration"
date: 2013-09-15 19:29
comments: true
categories: [Ruby, Rails, Migrations]
---
While working on a Rails 4 application, I came across a new addition to migrations `add_reference`. `add_reference` makes it easier to create migrations for foreign keys.

## Rails 3

In Rails 3, to add a foreign key and an index you would write a migration like this:

```ruby
class AddUserToBooks < ActiveRecord::Migration
  def change
    add_column :books, :user_id, :integer
    add_index  :books, :user_id
  end
end
```

It requires two method calls, one to add the `user_id` column to the Books table, and another to add an index to `user_id`.

## Rails 4

In Rails 4, you can accomplish the same thing with `add_reference`:

```ruby
class AddUserToBooks < ActiveRecord::Migration
  def change
    add_reference :books, :user, index: true
  end
end
```
{% pullquote %}
While it isn't a major change, it further illustrates the foundation that Ruby was built upon. {" "Ruby is designed to make programmers happy." - Yukihiro Matsumoto "}
{% endpullquote %}
