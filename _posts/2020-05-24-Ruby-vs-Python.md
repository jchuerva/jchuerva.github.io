---
title: Ruby vs Python syntax
date: 2020-05-24 16:00:00 +01:00
tags: [ruby, python]
description: Ruby vs Python syntax
---


### Printing and String Interpolation

Ruby:
```ruby
puts "value of a: %s" % a
# value of a: test
puts "value of a: #{a}"
# value of a: test
```

Python:
```python
print "value of a: %s" % a
# value of a: test
print "value of a: {0}".format(a)
# value of a: test

print(f”{I}. {key} appears {wordBank[key]} times.”)
```


### Nothing and Truthiness

Ruby:
```ruby
my_var.nil?
```

Python:
```python
my_var is None
```


### Boolean

Ruby:
```ruby
!false # true

# these short-circuit:
false && true # false
true || false # true
```

Python:
```python
not False # True

# these short-circuit:
False and True # False
True or False # True
```


### Arrays/Lists

#### Instantiation

Ruby:
```ruby
a = []
b = [1,2,3]
c = Array.new
d = Array[1,2,3]
e = (1..5).to_a
```

Python:
```python
a = []
b = [1,2,3]
c = list()
d = list([1,2,3])
e = list(range(5))
```

#### Operations

Ruby:
```ruby
a = [1,2]; b = [3,3]
a.include?(1) # true
a + b # [1,2,3,3]

# makes shallow copies:
[a] * 2 # [[1,2],[1,2]]

a[0] # 1
a.first # 1
a[-1] # 2
a.last # 2
a.size # 2
a.length # 2
a.count # 2
b.index(3) # 0
b.count(3) # 2
```

Python:
```python
a = [1,2]; b = [3,3]
1 in a # True
a + b # [1,2,3,3]

# makes shallow copies:
[a] * 2 # [[1,2],[1,2]]

a[0] # 1

a[-1] # 2

len(a) # 2


b.index(3) # 0
b.count(3) # 2
```

#### Slicing

Ruby:
```ruby
vals = [a,b,c,d]
vals[1..2] # [b,c]
vals[1...3] # [b,c]
vals[-3..-2] # [b,c]
vals[-3...-1] # [b,c]

# alternate form:
# array[start_index, length]
vals[1,2] # [b,c]
vals[-3,2] # [b,c]
```

Python:
```python
vals = [a,b,c,d]
vals[1:3] # [b,c]

vals[-3:-1] # [b,c]
```

Slicing to/from the end of list:

Ruby:
```ruby
vals = [a,b,c,d]
vals[2...vals.size] # [c,d]
vals[2,vals.size] # [c,d]
vals.last(vals.size - 2) # [c,d]
vals[0...2] # [a,b]
vals[0,2] # [a,b]
vals.first(2) # [a,b]
```

Python:
```python
vals = [a,b,c,d]
vals[2:] # [c,d]


vals[:2] # [a,b]
```

### Hashes/Dicts

Ruby:
```ruby
a = {}
b = {'x'=>1, 2=>2, Fixnum=>3}
c = Hash.new
d = Hash[[['x',1],[2,2]]]
e = Hash.new { |hash,key| hash[key] = [] }
# Blocks! Getting ahead of myself...
```

Python:
```python
a = {}
b = {'x':1, 2:2, int:3}
c = dict()
d = dict([['x',1],[2,2]])
e = defaultdict(list) # from collections module
```


Ruby:
```ruby
a = {'x'=>2}
a['y'] = 5
a.has_key?('y') # true
a.key?('y') # true
a.delete('y') # 5
!a.include?('y') # true
a.keys # ['x']
a.values # [2]
a.to_a # [['x',2]]
```

Python:
```python
a = {'x':2}
a['y'] = 5
'y' in a # True

del a['y']
'y' not in a # True
a.keys() # ['x']
a.values() # [2]
a.items() # [('x',2)]
```

### Symbols

Ruby:
```ruby
:foo.to_s # "foo"
"foo".intern # :foo
"foo".to_sym # :foo
:foo == :foo # true
:foo.object_id == :foo.object_id # true
```


### Control Flow

#### If-block

Ruby:
```ruby
if true
  puts "Gets here"
elsif false
  puts "Doesn't get here"
else
  puts "Definitely not getting here"
end
```

Python:
```python
if True:
  print "Gets here"
elif False:
  print "Doesn't get here"
else:
  print "Definitely
```

Inline if:

Ruby:
```ruby
if answer then "right" else "wrong" end
(answer) ? "right" : "wrong"
puts "You're right!" if answer
puts "You're right!" unless !answer
```

Python:
```python
"right" if answer else "wrong"

print "You're right!" if answer
```

#### For-loop

Ruby:
```ruby
for i in 0..2
  puts i
end
(0..2).each { |i| puts i }
```

Python:
```python
for i in range(3):
  print i
```

#### While-loop

Ruby:
```ruby
while true
  # do stuff
end
until false
  # do stuff
end
```

Python:
```python
while True:
  # do stuff
```

### Methods/Functions

Ruby:
```ruby
def myfunc
  puts "Hello!"
end
```

Python:
```python
def myfunc():
  print "Hello!"
```


Ruby:
```ruby
def myfunc
  1 + 1
end
myfunc # 2

def myfunc2
  return "foo"
  "nope"
end
myfunc # "foo"

```

Python:
```python
def myfunc():
  1 + 1

myfunc # None

def myfunc2():
  return "foo"

myfunc # "foo"
```


Ruby:
```ruby
def myfunc(x, y=2)
  x + y
end
myfunc(2) # 4
myfunc(2,3) # 5
```

Python:
```python
def myfunc(x, y=2):
  return x + y

myfunc(2) # 4
myfunc(2,3) # 5
```

Extra arguments as array

Ruby:
```ruby
def myfunc(x, y=2, *args)
  args
end
myfunc(1, 2, 3, 4, 5) # [3,4,5]
```

Python:
```python
def myfunc(x, y=2, *args):
  return args

myfunc(1, 2, 3, 4, 5) # (3,4,5)
```


Ruby:
```ruby
def myfunc(req, optional=2, *args,
           foo: 'bar', **options)
  p args
  p foo
  p options
end
myfunc(1, 2, 3, hey:'there', hi:3)
# [3]
# "bar"
# {:hey=>"there", :hi=>3}
```

Python:
```python
def myfunc(req, optional=2, *args, **kwargs):
  print args
  print kwargs

myfunc(1,2,3, hey='there', hi=3)
# (3,)
# {'hi': 3, 'hey': 'there'}
```


### Blocks, Procs, Lambdas

Ruby:
```ruby
def foreach(arr, &block)
  puts "block class: #{block.class}"
  for i in 0...arr.size
    block.call(arr[i]) # passes arr[i] to the block
  end
end

foreach([1,2,3]) { |x| puts x }
# block class: Proc
# 1
# 2
# 3
```

Python:
```python
def foreach(arr, myfunc):
  for i in range(len(arr)):
    myfunc(arr[i])

foreach([1,2,3], lambda x: print x)
# this is the equivalent of the Ruby code but
# won't work because lambdas in Python must
# contain 1 expression -- a SyntaxError is
# thrown when they contain statements like "print"
```


Ruby:
```ruby
double = Proc.new { |x| x * 2 }
double.call(3) # 6
```

Python:
```python
double = lambda x: x * 2
double(3) # 6
```

#### Iterating

Ruby:
```ruby
[1,2,3].each { |x| puts x }
# 1
# 2
# 3

[:a,:b,:c].each_with_index do
  |letter, index| puts "#{index} #{letter.inspect}"
end
# 0 :a
# 1 :b
# 2 :c
```

Python:
```python
for x in [1,2,3]:
  print x
# 1
# 2
# 3

for index, letter in enumerate(['a','b','c']):
  print index, letter
# 0 a
# 1 b
# 2 c
```

#### Mapping

Ruby:
```ruby
a = [1,2,3]
a.map { |x| x**2 } # [1,4,9]
# map returns a new array
# use map! to mutate the original

a.collect { |x| x**2 } # [1,4,9]
# collect is an alias of map
```

Python:
```python
a = [1,2,3]
[x**2 for x in a] # [1,4,9]
map(lambda x: x**2, a) # [1,4,9]
```

#### Reducing

Ruby:
```ruby
a = [1,2,3]

a.reduce(:+) # 6 (uses :+ method on Fixnum)
a.reduce(10, :+) # 16 (initial value of 10)
a.reduce(10) { |sum,x| sum + x } # 16
# inject is an alias of reduce
```

Python:
```python
a = [1,2,3]
import operator
reduce(operator.add, a) # 6
reduce(operator.add, a, 10) # 16
reduce(lambda sum,x: sum + x, a, 10) # 16
# unfortunately there's no pleasant way to
# reduce with a comprehension
```

#### Filtering

Ruby:
```ruby
a = [1,2,3]
a.select { |x| x.even? } # [2]
a.find_all { |x| x.odd? } # [1,3]
```

Python:
```python
a = [1,2,3]
[x for x in a if x % 2 == 0] # [2]
filter(lambda x: x % 2 == 1, a) # [1,3]
```


Ruby:
```ruby
# using \ to write across multiple lines

(0...20).select { |x| x.odd? } \
           .collect { |x| x**3 } \
           .inject(0, :+)
# 19900
```

Python:
```python
# using \ to write across multiple lines
import operator
reduce(operator.add, \
          [x**3 for x in range(20) \
          if x % 2 == 1], 0)
# 19900
```

### Classes

Ruby:
```ruby
class Person
  attr_reader :name # getter for name
  attr_accessor :mood # getter/setter for mood
  @@count = 0 # class var

  # constructor
  def initialize(name, mood=:happy)
    @name = name # instance var
    @mood = mood # instance var
    @@count += 1
  end
  
  def self.count # class method
    @@count
  end
end

Person.count # 0
bob = Person.new('Bob')
bob.mood # :happy
Person.count # 1
amy = Person.new('Amy')
Person.count # 2
```

Python:
```python
class Person:
  count = 0 # class var
  
  # constructor
  # all instance methods have 'self' as 1st param
  def __init__(self, name, mood='happy'):
    self.name = name # instance var
    self.mood = mood # instance var
    Person.count += 1

  # class methods have 'cls' as 1st param
  @classmethod
  def get_count(cls):
    return cls.count
    

Person.count # 0
bob = Person('Bob')
bob.mood # 'happy'
Person.count # 1
amy = Person('Amy')
Person.get_count() # 2
```


