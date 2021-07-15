---
title: Private methods in Ruby
date: 2021-07-15 10:00:00 +01:00
tags: [ruby]
description: Private methods in Ruby
---

Let's start with the basic solution, that define all `private` methods under `private` and at the end of the class definition.

```ruby
class Foo
  def bar
    :awesome
  end
​
  private
​
  def baz
    :something_private
  end
end
```

All methods under `private` are defined as `private`

We can define each individual method instead:

```ruby
class Foo
  def bar
    :awesome
  end
​
  private def baz
    :something_private
  end
end
```

Note: By default, a class is `public`

We can always change the method properties after define them

```ruby
class Example
 def xmethod
 end

 def ymethod
 end

 def zmethod 
 end

 private :xmethod, :zmethod

end
```

Same for a class `attr`:

```ruby
class Foo
  private attr_accessor :awesome_variable
end
```

### Sources: 
- https://thecodest.co/blog/ruby-3-0-ruby-and-lesser-known-privacy-control-methods/
- https://stackoverflow.com/questions/10724221/where-to-place-private-methods-in-ruby
- http://ruby-for-beginners.rubymonstas.org/advanced/private_methods.html
