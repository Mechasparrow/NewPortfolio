---
layout: post
title: Web Backend Development with Ruby (Rails) Part two
tags: Development
categories: tutorial
crosspost_to_medium: true
excerpt_separator:  <!--more-->
---

If you haven't read part one. Go back and [read that](https://medium.com/@mechasparrow/web-backend-development-with-ruby-rails-part-one-ae4cf818e546)

In this tutorial we will create additional models and seed them into the database.

After that we will write a simple view that will display a list of items the the grocery store has in stock.

## Lets make the application look nicer

Before we start adding more data models to our application, lets install bootstrap.

inside the file named `Gemfile`, add these lines to it

```ruby
gem 'bootstrap', '~> 4.1.3'
gem 'jquery-rails'
```

Then run

`bundle install`

This will install the bootstrap library for the project.

Afterwards go into the `/app/assets/stylesheets/application.scss` file and add these line

```css
/*
Adds bootstrap
*/

@import "bootstrap";
```

if the `application.scss` file ends in `.css`, just change it so it has the `.scss` ending.

You can do this by running this command to change it in the terminal

`mv app/assets/stylesheets/application.css app/assets/stylesheets/application.scss`

Add these lines to `/app/assets/javascripts/application.js` file.

```javascript
//= require jquery3
//= require popper
//= require bootstrap
```

Now we have Bootstrap 4 added to our application!

## Adding our new models

We are going to add 2 new models, `Ordered_Items` and `Carts`. The ordered items model will be a table of items that have been ordered. The Cart will contain items that were ordered by the customer. So there will be some level of overlap that we will have to account for.

These are called associations.

First lets create the new models.

`rails generate model Cart`
`rails generate model OrderedItem`

inside the folder `/db/migrate`, there will be a file ends with `create_ordered_items.rb`, open that file and edit it so it becomes this.

```ruby
class CreateOrderedItems < ActiveRecord::Migration[5.1]
  def change
    create_table :ordered_items do |t|
      t.integer :quantity
      t.references :item, index: true
      t.references :cart, index: true
      t.timestamps
    end
  end
end
```

The `references` fields refers to the tables that the the table is connected to. In this case it is the `Item` and `Cart` model. I'll show you how this connects when we seed example data for this.

The last migration we need to create is in order to modify our previous `Item` table. To do this we run.

`rails generate migration AddStockToItems`

This will create a file in the `migrations` folder that ends with `add_stock_to_items.rb`. Edit the file so it contains this...

```ruby
class AddStockToItems < ActiveRecord::Migration[5.1]
  def change
    add_column :items, :stock, :integer
  end
end
```

With the `add_column` function, this migration adds a integer(number) column called `stock` to the `items` table.

And finally.

`rails db:migrate`

## Writing the model associations

In order to handle the connected tables we need to write the corresponding associations.

Inside `/app/models/ordered_item.rb`

```ruby
class OrderedItem < ApplicationRecord
  belongs_to :item
  belongs_to :cart
end
```

Inside `/app/models/item.rb`

```ruby
class Item < ApplicationRecord
  has_many :ordered_items, dependent: :destroy
end
```

Inside `/app/models/cart.rb`

```ruby
class Cart < ApplicationRecord
  has_many :ordered_items, dependent: :destroy
end
```

With the `belongs_to` and `has_many` association functions. We tell the framework that a Item has many Ordered Items connected to it. The Cart also has many ordered_items connected to it. And the Ordered Item belongs to both a Item and Cart.

That way when a customer wants to take his or her cart to checkout, it already has all the corresponding ordered items. Those ordered items point to the items that are currently in stock.

This may sound over complicated, but it will make managing the item inventory much more easier :)

## Seeding the data

If we want to display the items we have in stock, we need some items!

Go into `/db/seeds.rb`, and edit it so it is like this.

```ruby
items = Item.create([
  {
    name: 'Apple',
    description: 'Basic Fruit',
    price: 0.25,
    stock: 1
  },
  {
    name: 'Watermellon',
    description: 'Fruit mainly composed of Water',
    price: 3.00,
    stock: 1
  }
])
```

run
`rails db:seed`

to add the new data to the database.

Now we have some initial items that we can display.

## Displaying the item catalog

So now we want to display the items in the shop. Go back to the InventoryController (`/app/assets/controllers/inventory_controller.rb`) and edit the `index` function like so

```ruby
...
def index
  @items = Item.all
end
...
```

We created a variable called `@items` that we will be able to reference to within our view template. `Item.all` retrieves all the rows from the Item model.

Let's edit our view to be able to use this variable.
Go into `/app/views/inventory/index.html.erb` and change it to this

```html
<div class = "container">
  <h1 class = "header-title"> Here are all the items that are currently in stock...</h1>

  <% if (@items.length <= 0) %>
    <p>Sorry no items :-( </p>
  <% end %>

  <% @items.each do |item| %>
    <div class = "card card-item">
      <div class = "card-body">
        <h5 class = "card-title"><%= item.name %></h5>
        <h6 class = "card-subtitle mb-2 text-muted"><%= item.stock %> in stock</h6>
        <p class = "card-text">
          <%= item.description %>
        </p>
        <p class = "card-text card-item-cost">
          $<%= '%.2f' % item.price %>
        </p>
      </div>
    </div>
  <% end %>


</div>
```

also edit `/app/assets/stylesheets/inventory.scss` to contain this

```css
.header-title {
  margin-top: 16px;
}

.card-item {
  margin-top: 16px;
}

.card-item-cost {
  color: green;
}
```

Now when you visit the `/` of the rails app. It should display something like this.

![](https://i.imgur.com/FnKzm5G.png)

Great Job! Now people can see what items can be purchased! In the next tutorial, we will start adding functionality that will allow the user to add items to his/her cart.

You can get the source code for this project on [Github](https://github.com/Mechasparrow/inventory-manager-rails-tutorial)

-- Mechasparrow
