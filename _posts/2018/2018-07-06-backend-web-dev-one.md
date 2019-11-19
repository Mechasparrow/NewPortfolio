---
layout: post
title: Web Backend Development with Ruby (Rails) Part one
tags: Development
categories: tutorial
crosspost_to_medium: true
excerpt_separator:  <!--more-->
---

## What is an web backend ##

A backend is the piece of software that deals with the business logic for interacting with the application's data. This could be tweets, account records, facebook posts, and any other sort of data that you could think of that a user would want to interact with.

In a later tutorial we will go over frontend development to help shape what the user sees.

## What are we going to build? ##

We are going to build a simple store inventory system. That way a grocery manager/clerk can keep track of what items are in stock. This tutorial will be separated into different parts. In this one we are going to just set up rails and generate some basic code to start.

## Installing Rails ##

Make sure that you have [Ruby](https://www.ruby-lang.org/en/) installed. Once you do we are going to run this command in the terminal

`gem install rails`

This installs the latest version of rails that we will be used for developing our store inventory software.

Alright it is time to create our application.

`rails new inventory-system`  
`cd inventory-system`  
`bundle install`  

This creates our new application in a folder called `inventory-system` and then we move into that folder and install our libs with the `bundle install` command.

From this point on, when I refer to `/`, I mean the path to the `inventory-system` project folder.

## Launching the application ##

You can launch the application with

`rails s`

Then navigate your web browser to `localhost:3000`

You should see something like this..

![Your on rails](http://guides.rubyonrails.org/v5.0/images/getting_started/rails_welcome.png)

## Generating helper code ##

We are going to generate some code just to save some time.

### Generating the data model ###

A model is software layer that simplifies the process of saving and reading data from our database.

So for our `items` model. run

`rails generate model item name:string description:text price:decimal`  
`rails db:migrate`

This generates a model that has a name, description, and a price field. This will be important later on when we are saving or viewing item data.
We then run a migration with the second command so the database is changed in response to our model code we generated.

### Generating the controller ###

The controller is the code that is the real meat of the application. This is where all the business logic goes.

To generate our controller run

`rails generate Inventory`

This generates a controller (ruby file) that will deal with all the business logic for managing our inventory. Our inventory will just be a collection of `Item` data.

This will generate a file called `inventory_controller.rb`

in the file you'll see

create a function called `index`

``` ruby
def index

end
```

and then go into `/config/routes.rb`

and add the code as seen from the `new code` comment

``` ruby
Rails.application.routes.draw do
  # New code
  root 'inventory#index'

end
```

then create the file starting from root `/app/views/inventory/index.html.erb`

inside it write

``` html
<h1>Hello there</h1>
```

Now if we visit `localhost:3000`, we should see this simple web page with the text, **Hello There**

That's it for this tutorial. In the next one we are going to actually load some items into the database and render them to the client.

There will also be a separate tutorial on install bootstrap for this project.

You can get the source code for this project on [Github](https://github.com/Mechasparrow/inventory-manager-rails-tutorial)
