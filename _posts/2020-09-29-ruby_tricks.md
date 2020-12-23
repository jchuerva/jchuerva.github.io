---
title: Collection of Ruby/Rails tricks - ternary operator, mocha, controller method in models & guard cause
date: 2020-08-29 17:00:00 +01:00
tags: [ruby]
description: Collection of Ruby/Rails tricks - ternary operator, mocha, controller method in models & guard cause
---


## Ternary Operator alternative

```ruby
user_login = User.current ? User.current.login : nil
# as User.current could be nil, we can express it as:
user_login = User.current && User.current.login
# or simplify it as:
user_login = User.current&.login
```


## Mocha
Mocha commands to remember:
```ruby
# simple stubs and check(executed just one)
User.expects(:current).returns("TestUser")

# stub multiple times
BacktracesController.any_instance.expects(:authenticated?).returns(true)

# Stubs with
Bones.sentry.expects(:get_issue_raw_stacktrace).with("1").returns("test")

# Never
OpenFeatureFlagIssuesJob.any_instance.expects(:log).never

# Simple stubs (without check it)
Bones::Config.stubs(:bones_authentication_token).returns("fail")

```

## Controller method availabe in Model
```ruby
# model
class Account < ActiveRecord::Base
  cattr_accessor :current
end

# controller
class ApplicationController < ActionController::Base
  before_filter :set_current_account
  def set_current_account
    #  set @current_account from session data here
    Account.current = @current_account
  end
end
```

## Guard cause

A good use for unless is when you want to check something at the beginning of a method. Also known as a guard clause.
```ruby
def my_method(name)
  return unless name.empty?
  # ...
end

# value
my_method() # => ni
```