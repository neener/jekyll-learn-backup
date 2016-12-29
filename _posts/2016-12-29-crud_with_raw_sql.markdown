---
layout: post
title:  "CRUD with raw SQL"
date:   2016-12-28 21:34:57 -0500
---


After many labs using ActiveRecord, I started to rely heavily on its powerful methods. I started to use ActiveRecord methods as just the right way to map database tables to Ruby classes without thinking about how these methods were abstracting away raw SQL queries. A refresher on how to CRUD with raw SQL is in order. I’ll use example code from my Sinatra project for CRUDing a Category object (with a database table of ‘categories’) along with the raw SQL statements.

**Create - SQL Inserting**

Two common methods for creating an object are the Ruby method `.new` and ActiveRecord method `.create`

```
@category = Category.new(category_params)
@category.save
```

and

`@category = Category.create(category_params)`

The `.new` method prepares the object for new params but does not save the params to the database until `.save` is called. This is useful if you want to check if certain data is permitted to be inserted into the database. The` .create` method instantiates, initializes and saves the object to the database all in one method call.

The SQL query for Creating a new object in the database looks like this:

`INSERT INTO categories (title) VALUES (‘your_category_title’);`

when` .new` is called on the Category class, in SQL we are saying INSERT INTO the ‘categories’ table with the column name of ‘title’ with the VALUE that is being provided.

**Read - SQL Selecting**

To view or read from the database we can call the ActiveRecord `.all` method on our class object. 

```
@categories = Category.all
```

The SQL query for this would look like:

`SELECT * FROM categories;`

Using * (or wildcard) allows us to select all the data from all of the columns in the categories table.


**Update - SQL Updating**

The ActiveRecord `.update` method is used along with a hidden request form to update or change data.

```
@category = Category.find_by_id(params[:id])
@category.update(category_params)
```

In my case I am setting the instance variable @category to its associated id. Then I will update that specific category with new category_params, such as changing the title.

The SQL for `.find_by_id` would look like:

`SELECT * FROM categories WHERE id = params[:id];`

The SQL query for updating this object would look like:

`UPDATE categories SET title = “your_category_title_new” WHERE title = “your_category_title_old”;`

**Delete - SQL Deleting**

Deleting an entry is similar to updating a an entry, which also needs a hidden request form. You locate the class by its specific parameters, In my case ‘id’, and then use the ActiveRecord method `.destroy` to erase it from your database. 

```
@category = Category.find_by_id(params[:id])
@category.destroy
```

The SQL query for deleting this object would look like:

`DELETE FROM categories WHERE id = params[:id];`


