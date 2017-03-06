---
layout: post
title:  "Using OmniAuth Github with Devise"
date:   2017-03-06 12:13:42 +0000
---

**Step 1**

Set up your oauth application on the provider service, I chose Github.

Navigate to your github account, go to profile, settings, oauth applications.

Next I create an .env in my root directory. Setting up an .env file is a fairly standard way of what is called provisioning secrets to your application.

You put your secret keys in your .env file.

```
export GITHUB_APP_ID='your_app_id'
export GITHUB_APP_SECRET='your_app_secret'
```

Export it into your shell sessions, when you run the application they are part of the applications memory.

but the way we are doing this is the next step:

in your Gemfile 

install:

```
gem 'dotenv-rails'
```

This gem automates reading that file every time you start your rails application and adding the environment variables from there to the env hash (the global variable that is available in ruby). 

You can also do this manually in the conosle with:

`‘source .env && rails s’`

There is a command called source, and when you do source on a file like, source .env, it reads that file with bash and any variables you set in that file get set to the current shell session.

That is essentially was .env does for you, it reads that file with a similar command then starts your application.

It loads configuration and starts your server all at once.

The dotenv gem - hooks into the start up cycle of rails and before rails loads any of its own files it makes sure your .env file is loaded so you don’t have to worry about it

**Step 2**

install `gem 'omniauth-github'` in gem file

This provides the underlying functionality that you need to read an omniauth response from Github.

Each oauth provider is going to send you different information about the user. (permission, options, etc) when you do an oauth request to them.

So that gem just contains all that stuff for Github, how to ask for something (the name, the email)

**Step 3**

you go to the user model

add omniauthable - which is a strategy for login and then tell it that the omniauth_providers that works for this is github

```
devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable, :omniauthable, :omniauth_providers => [:github]
```

**Step 4** 

in config/initializers/devise.rb 

added config.omniauth part - 

`config.omniauth :github, Rails.application.secrets.github_app_id, Rails.application.secrets.github_app_secret, scope: 'user,public_repo'`

That says Github can auth with this, then your client id is `Rails.application.secrets.github_app_id` value in secrets and the secret itself is `Rails.application.secrets.github_app_secret` value in secrets 

**Step 5**

Create controller users/omniauth_callbacks_controller.rb

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
	def github
	    # You need to implement the method below in your model (e.g. app/models/user.rb)
	    @user = User.from_omniauth(request.env["omniauth.auth"])

	    if @user.persisted?
	      sign_in_and_redirect @user, :event => :authentication #this will throw if @user is not activated
	      set_flash_message(:notice, :success, :kind => "Github") if is_navigational_format?
	    else
	      session["devise.github_data"] = request.env["omniauth.auth"]
	      redirect_to new_user_registration_url
	    end
  	end

  	def failure
    	redirect_to root_path
  	end
end
```

When you sign up or sign in with Github the first thing it does is try to look you up with the information passed back to you by Github, once you get your response from github with the user information in `request.env[“omniauth.auth”]`

Once it has found the user it says if that user exists already sign them in and redirect them to whatever page they were going to.

If its not an API response that is being sent thats what `‘is_navigation_format?’` checks for. Are we responding with a format that someone is going to see or are we responding with a JSON object?

If we are responding with a format that someone is going to see, add in the success message to the flash so you can display that

If the user doesn’t exist, you redirect them to the user registration page and you can register with oauth instead.

The omniauth github gem has all the stuff inside of it already needed to register a user from omniauth.

This will only come up if there is an error when saving the user when your signing up.

The failure methods says if at any point in the process the user fails to sign up, wrong password, not registered etc. redirect them to the rootpath.

Last Step - 

I added a users/registrations_controller.rb to add sanitization for the name attribute.

```
class Users::RegistrationsController < Devise::RegistrationsController
  before_filter :configure_permitted_parameters

  protected

  def configure_permitted_parameters
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name])
  end
end
```

In the routes file we tell devise we want to use that controller for all the user registrations. 

We are telling it to inherit from the regular devise registrations controller, so we have all of the code that comes with the registrations controller, we are just adding the addition permitted parameters to it.



