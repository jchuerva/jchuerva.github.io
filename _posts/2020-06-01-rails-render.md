---
title: Rails - render vs redirect_to
date: 2020-06-01 07:00:00 +01:00
tags: [ruby, rails]
description: Rails - render vs redirect_to
---

How to return and redirect from the controller to the view:

```ruby
# ruby controller
def boomtown
  # return json
  render json: {key: "value"}

  # return plain text
  render plain: "Response using plain text"

  # redirect_to
  redirect_to action_name_resource_path #important to mention the path

  # render specific view
  render :index

  # by default it search the view with the same name as the controller. Eg:
  # render :boomtown
end
```

### Params:

- render

It takes the global params defined in the controller `@parameter_whatever`

- redirect_to

We can pass parameters but they will visible as url parameters:

```
redirect_to action_name_resource_path(resource_object, param_1: 'value_1', param_2: 'value_2')
```

### Links

From [ruby on rails documentation](https://guides.rubyonrails.org/layouts_and_rendering.html)