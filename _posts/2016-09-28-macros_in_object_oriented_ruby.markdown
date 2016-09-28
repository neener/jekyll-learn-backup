---
layout: post
title:  "Macros in Object Oriented Ruby"
date:   2016-09-28 00:10:34 -0400
---


The first thing I noticed about the Learn curriculum was that all the labs were test-driven. This made a huge impact on me when I was trying out the Intro to Javascript Track, before being accepted into the program. Writing tests is the ultimate proof you know whats going on and your code is doing what you think it is. With that said after watching the [Collaborating Objects - Pure OO w/ TDD](https://www.youtube.com/watch?v=iYcQ693LXck) lecture, being able to write my own tests I realized won’t come easily. Thankfully I’ve been looking at tests since I started Learn, but creating them from scratch is a totally different ballgame. 

There have been a few learning tactics I have developed during the Learn course so far. One is dictating my code as I go, but also revisiting old code and dictating from start to finish, in plain english, what is going on. Sometimes I even speak it out loud while I’m typing what the code is doing. I have also learned that I need to watch (at least twice) and carefully take notes during each youtube review, and the first time around I will only understand 25-40% of whats happening, but without fail, and a few labs in between, the second time around I understand more like 60-85% of whats happening. This drastic increase in understanding proves that perseverance does pay off and really helps me stay motivated. The switch from not understanding to understanding, usually doesn't take as long as it feels it might. 

With that said, I have been nervous to write my second blogpost, about code. I’m not sure how to write about code, or that my beginner skills can really elaborate on too many details, so I thought a combination of highlighting points from my dictated code and bringing up some key points from my notes on the youtube lectures would be a good place to get the ball rolling. More specifically I thought highlighting some of the Macros I have learned so far would be a good place to pinpoint my understanding of what is happening under the hood or through abstraction. Here is a recap of learning Object Oriented Ruby through Macros.

First, In Plain English, what are the core elements of Ruby? 

@ THE RUBY CORE:

	Objects
	Methods
	Classes

- everything in Ruby is an **object**
- an **object** is all of the data(variables) and all of the **behaviors**(methods) needed in order to accomplish a job

- **methods** are behaviors that an **object** performs upon its internal data and even upon other code objects
- the only thing you can do in Ruby, is ***call methods on objects***

- each **object** in Ruby has a **class**
- classes behave like blueprints (they define what objects can do) and factories (they manufacture objects or bring them to life)


What is a Macro? - its a shortcut - or any piece of code that writes code for you. Macros allow your program to become more abstract, by removing or concealing verbosity.

**attr_accessor**

attr_accessor is the first macro we are introduced to explicitly. 

Every object can either read what object has already been created (with a reader method) or it can write a new object into being (with a writer method), or both.

attr_accessor sets an object with a setter method and a getter method, through symbol notation. For example:

```
attr_accessor :name :breed
```

The above code creates 4 methods, a getter and a setter for both name and breed. In long form It would look like this:

```
class Dog

def name
   @name
end

def name=(new_name)
   @name = new_name
end

def breed
   @breed
end

def breed=(new_breed)
   @breed = new_breed
end

end
```

The attr_accesssor, abstracts the above code into a very short and sweet line of code. If the above looks verbose comparatively we can also be more explicit about the method attr_accessor which is even further abstracted from the above code. This abstraction can be referred to as metaprogramming.

**implicit and explicit receivers - metaprogramming (self)**

metaprogramming -> each object in ruby has its own metaclass, a Class that can have methods, but is only attached to the object itself. The metaclass is invisible in Ruby. -[ Yehuda Katz](http://yehudakatz.com/2009/11/15/metaprogramming-in-ruby-its-all-about-the-self/)

self is the implicit receiver of every single method call your sending

whenever you don’t see a receiver, the implicit receiver of ruby is always self

The above code describing attr_accessor is actually much more verbose than writing out two method definitions for every new object.  It was fun to see how verbose the heart of the attr_accessor actually is from the [Intro to Object Orientation - Q/A](https://www.youtube.com/watch?v=ab11lJJKm8M)

The following code is what gives attr_accessor its functionality.

```
class Dog
  def self.define_properties(props)
    props.each do |prop|
      define_method(prop) do 
        instance_variable_get(:"@#{prop}")
      end

      define_method("#{prop}=") do |arg|
        instance_variable_set(:"@#{prop}", arg)
      end
    end
  end

  self.define_properties([:name, :breed])
end
```

The above code is defining the reader. The reader exposes the instance variable. It then defines the writer. The writer exposes the instance variable. 

**nothing is what it seems**

```
1 + 1
```

following the rule that "The only thing I can do in Ruby, is call methods on objects" - [Collaborating Objects Pure OO w/ TDD](https://www.youtube.com/watch?v=iYcQ693LXck)

The above code does *not* read: the integer 1 plus the integer 1. What you don't see in the above equation, that the Ruby language implicitiy implies is this equation :

```
1.+(1)
```

This equation actually reads: one dot plus the method where the argument of that method is the other integer.

So far, I am learning that it is crucial to know exactly what you are saying with code and check what your objects are actually containing in irb and pry. These tools are helpful for understanding what is happening with your code. Because Ruby uses many implicit receivers, it is crucial to always fact check your code and be aware that the foundation of every method is more complex that you might initially assume. You don't just get an attr_accessor out of nowhere and then you can use it, it is birthed out of something you don't completely understand. 
