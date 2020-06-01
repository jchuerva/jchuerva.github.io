---
title: Rails - Add a migration to populate previous table column
date: 2020-05-17 12:00:00 +01:00
tags: [rails, db]
description: Rails - Add a migration to populate previous table column
---

This week, I created a new Rails migration to add a the new column: `short_sha` in the `imports` table:

```ruby
class AddShortSha < ActiveRecord::Migration[6.0]
  def change
    add_column :imports, :short_sha, :string
  end
end
```

This new `short_sha` value will be automatically calculate for new `Imports`, but what about previous one?
Rails allow us to add some method to also update the previous `Imports` as part of the migration:

```ruby
class AddShortSha < ActiveRecord::Migration[6.0]
  def change
    add_column :imports, :short_sha, :string
    Import.all.each(&:calculate_short_sha)
  end
end
```

Easy and great Rails functionality 🍻


