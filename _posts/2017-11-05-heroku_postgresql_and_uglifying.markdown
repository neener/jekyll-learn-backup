---
layout: post
title:      "Heroku, Postgresql & Uglifying"
date:       2017-11-06 04:29:00 +0000
permalink:  heroku_postgresql_and_uglifying
---


A little recap of some issues I ran into while deploying my Polling App to Heroku.

I haven’t deployed an App in a few years and was pleasantly surprised to see how much easier the Heroku interface is, but thats just a nice sugar coating. 

Since I haven’t visited my Heroku profile in quite sometime. I was  pleasantly surprised to see some old projects I had forgotten about. Namely this funny blog experiment I made many years ago: [blog app experiment](https://dry-tor-88063.herokuapp.com/) Not much depth to this App, but a funny experiment to stumble upon. 

The Recap:

The first issue I ran into while trying to deploy was that I was still using sqlite3 as my db. I really don’t understand why sqlite3 comes prepackaged with Rails, since you can’t use that db in production. I’m sure there are many blogposts about this frustration across the Rails community. 

This was a pretty simple fix

In my gem file I got rid of the sqlite3 gem and replaced it with the postgres gem

```
gem ’pg’
```

in my database.yml file I also changed everything to use postgresql

```
default: &default
  adapter: postgresql
  pool: 5
  timeout: 5000

development:
  <<: *default
  database: pollmatic_app_development

test:
  <<: *default
  database: pollmatic_app_test

production:
  <<: *default
  database: pollmatic_app_production
```

then bundled.

That took care of the database issue. 

The next problem I ran into had to do with the uglfier precompiler

I knew the error was pointing to a JS file, but didn’t give me a file or code line to further find out where the error was coming from.

The error was 

```
ExecJS::RuntimeError: SyntaxError: Unexpected token: punc ())
```

some stack overflows where helpful to try to debug

[Stackoverflow punc() error](https://stackoverflow.com/questions/30422437/execjsprogramerror-unexpected-token-punc-expected-punc-when-running)

[Stackoverflow precompile error](https://stackoverflow.com/questions/12574977/rake-assetsprecompile-gives-punc-error)

I tried running this in my console

```
JS_PATH = "app/assets/javascripts/**/*.js"; 
Dir[JS_PATH].each do |file_name|
	puts "\n#{file_name}"
	puts Uglifier.compile(File.read(file_name))
end
```

Hoping to find the file and line where the Uglifier was causing problems

That didn’t give me any more insight into the problem.

Since I knew the issue was coming from my main .js file I tried copying and pasting all my JS into a delinter, ( i used http://jshint.com/ ) but I still didn’t receive any errors.

I am still going to try to figure out what is wrong with my uglifier error, but in the mean time and since this app was pretty simple, I just commented out the uglifier in my production.rb

```
#config.assets.js_compressor = :uglifier
```

This solved the problem temporarily. (not advised for larger applications since not using a precompiler can slow down your application)

My next issue was another database error

I ran:

```
heroku run rake db:setup -a pollmatic
```

to run the db migrations on Heroku

I received this error:

```
PG::ConnectionBad: FATAL:  permission denied for database "postgres"
DETAIL:  User does not have CONNECT privilege.
```

this stack overflow was helpful for understanding how to set up the migrations on Heroku

[Stackoverflow connect privilege](https://stackoverflow.com/questions/18972829/rake-dbreset-user-does-not-have-connect-privilege)

the problem was solved by running:

```
 heroku run rake db:migrate -a pollmatic
```


AND SUCCESS! my first deployed app in years :)

you can view it here: http://pollmatic.herokuapp.com/

Although I still have not styled anything and there is a huge yellow banner at the top of the page to make sure I was adding images correctly, I’m looking forward to styling this app and hopefully it will have some use value in the near future.
