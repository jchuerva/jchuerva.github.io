---
title: Combine search, pagination and export in Rails
date: 2020-04-29 12:00:00 +01:00
tags: [rails]
description: Combine search, pagination and export in Rails.
---

Let's see how combine the search, pagination and export options in Rails.

For the pagination, I've decided to use the [kaminari](https://github.com/kaminari/kaminari) gem. It provides a very easy way to add pagination into Rails. I've used it over [will_paginate](https://github.com/mislav/will_paginate) as `kaminari` includes (for free) some methods for the `view` pages. It also has the posibility of use some [themes](https://github.com/amatsuda/kaminari_themes) to the pagination component displayed in the view.

Regarding the search and export, it will be done using `ruby` code.

Let's see the code!

In the ***view***: `app/views/bits/index.html.erb`
```erb
# search component
<%= form_tag({}, method: :get) do %>
  <%= text_field_tag(:path, params[:path]) %>
  <%= submit_tag 'Search' %>
<% end %>

<% @bits.each do |bit| %>
# do whatever you need here with the data
 ...
<% end %>

# pagination
<div>
  <%= paginate @bits %>
</div>

# export
<div id="export">
  <button><%= link_to("Export results in csv", bits_path(path: controller.params["path"], format: :csv)) %></button>
  <button><%= link_to("Export results in xls", bits_path(path: controller.params["path"], format: :xls)) %></button>
</div>
```

Note: Mention the `form_tag` has two parameters:
1. path
1. `method` used. Eg: `:get`

If you indicate the path (eg: `form_tag(bits_path, method: :get)`), you will get redirected to the `bits_path` in the model. In case you don't indicate any path (eg: `form_tag({}, method: :get)`), you are redirected to the same same path you currently are.


In the ***controller***: `app/controllers/bits_controller.rb`
```ruby
def index
  @bits = Bit.search(params)

  respond_to do |format|
    format.html { render :index }
    format.xls { send_data generate_xls(@bits), filename: "Export-#{Date.today}.xls" }
    format.csv { send_data generate_csv(@bits), filename: "Export-#{Date.today}.csv" }
  end
end
```

Where the methods `generate_xls` and `generate_csv` are defined as private methods in the controller.


In the ***model*** we define the method `search()`: `app/models/bit.rb`
```ruby
def self.search(params)
  search = params[:path]
  page = params[:page]

  search_query = all # include here

  if search && !search.empty?
    search_query = search_query.where("path LIKE ? ", "%#{search}%")
  end

  if params[:format]
    # export
    search_query.find_in_batches
  else
    # display it with pagination 
    search_query.page(page)
  end
end
```

