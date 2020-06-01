---
title: Rails - Differences between nil?, empty?, blank?, and present?
date: 2020-05-29 11:00:00 +01:00
tags: [ruby, rails]
description: Rails - Differences between nil?, empty?, blank?, and present?
---

## Index
- [nil?](#nil)
- [empty?](#empty)
- [blank?](#blank)
- [present?](#present)
- [Recap](#recap)

### nil?

`#nil?` is a **Ruby** method on the Object class. Since all classes inherit from this class, `#nil?` can be used on **any** object.

```ruby
nil.nil?
=> true

true.nil?
=> false

5.nil?
=> false

"".nil?
=> false

[].nil?
=> false
```

### empty?

`#empty?` is a **Ruby** method that can be used on **strings**, **arrays**, **hashes** and **sets**.

##### *strings*

```ruby
"".empty?
=> true

" ".empty?
=> false

"\t\n".empty?
=> false
```

##### *arrays, hashes and sets*

It returns `true` if they have **no elements**

```ruby
[].empty?
=> true

{}.empty?
=> true

require 'set'
Set.new.empty?
=> true
```

### blank?

`#blank?` is a **Rails** method. It operates on any object.

##### *strings*

```ruby
"".blank?
=> true

" ".blank?
=> true

"\n\t".blank?
=> true
```

##### *arrays, hashes and sets*

It works just like [`#empty?`](#arrays-hashes-and-sets)

### present?

`#present?` is a **Rails** method. It does the opposite of what `#blank?` does.

### Recap

| | #nil? | #empty? | #blank? | #present? |
| --- | --- | --- | --- | --- |
|5|	false| 	NoMethodError| 	false| 	true|
|"" |	false| 	true| 	true| 	false|
|" "| 	false| 	false| 	true| 	false|
|"\t\n"| 	false| 	false| 	true|	false|
|[]| 	false| 	true| 	true| 	false|
|["a"]| 	false| 	false| 	false| 	true|
|{}| 	false| 	true| 	true| 	false|
|{a: "b"}| 	false| 	false| 	false| 	true|
|Set.new| 	false| 	true| 	true| 	false|
|nil| 	true| 	NoMethodError| 	true| 	false|
|true| 	false| 	NoMethodError| 	false| 	true|
|false 	|false| 	NoMethodError| 	true| 	false|