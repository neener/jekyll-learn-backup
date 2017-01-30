---
layout: post
title:  "What is Devise Hiding?"
date:   2017-01-30 06:01:13 -0500
---


Devise is a gem that takes care of your authentication needs. 

**Authentication** - asks WHO someone is

not to be confused with 

**Authorization** - which asks whether a user should be able to do a particular thing. (For simple applications you can use the cancancan gem and for more complex applications you can use the pundit gem)

In order to authenticate a user you need to build a working login system. You can roll your own authentication system with the bcrypt gem, a SessionsController, and by building your own helper methods **or** you can use the Devise gem.

It is important to remember how to roll your own login system so when Devise generates a bunch of code automagically you have a better chance of understanding how to debug.

Devise abstracts away the ‘securing password process’, the ‘setting a session process’, and writing our own helper methods to check if a user is logged in. 

First, we no longer need to deal with storing a secure password for the user. 

**bcrypt** - 
is a gem that hashes and salts your password and stores it securely in your database. you must include `password_digest:string` in your user migration table and you must call `has_secure_password` in your User model. bcrypt also gives us the `.authenticate` method which we call on the user instance when a user is trying to login. 

the **SessionsController** - 
logs a user in. session data is stored in the users browser (server side) in what is called a cookie. 

To create a SessionsController, First we set add `get ‘/login’` and `post ‘/login’` in our routes.rb.

Then in the create method in the SessionsController is were we set a cookie on the users browser by setting the username in a session hash :

```
def create
	session[:username] = params[:username]
	redirect_to '/'
end
```

**helper methods** - 
in order to check if our ‘user signed in’ or if they are the ‘current user’ we would write helper methods, defined as such in the ApplicationController, so the rest of our controllers can access them:

```
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception

  helper_method :current_user, :logged_in?

  	def current_user
  		@current_user ||= User.find(session[:user_id]) if session[:user_id]
  	end

  	def logged_in?
  		!!current_user
  	end

end
```

Devise includes these helpers so you don’t have to write them.


Devise:

*generates routes for you 

*generates a file in ‘config/initializers/devise.rb’ where the best devise docs are located

*handles secure passwords 

*comes with prebuilt helper methods such as `user_signed_in?` and `current_user`

*is made up of modules that are applied to your User model (or what a User account can have). They are set in the User model like this:

```
class User < ActiveRecord::Base
	devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
end
```
