---
layout: post
title:  "CLI GEM using Nokogiri Scraper"
date:   2016-10-18 17:41:35 -0400
---



I would like to talk about a couple issues I ran into while building this gem, but first I want to give a big shoutout to the Learn community. This project, with only one advisor and being unable to ask tutors for help, left me with two options, the internet at large and the other students in Learn. This was the first time I directly chatted with individual students who were willing to take hours to help me debug. This kind of teaching to learn has really struck me as an invaluable part of coding. Spending hours on helping someone else debug their project (not your own) is viewed as not a “waste of my time” or “I could be forwarding my own projects”, but legitimately as valuable for the teacher and the student. I guess this is obvious, but when put into practice I was really comforted by the amount of support I received from others who were just trying to get better and understand how things are working on a deeper level. THANK YOU!

The first problem I ran into was something described to me as ‘the flow of the program’ visualizing how the code is running and when your classes and methods are asking for things or being called. In this case my first problem was when I started scraping Wikipedia using Nokogiri. I wanted to scrape a list of urls, assign them a number and then ask the user for a number to which a new method would be called grabbing each unique url’s info. 

I was able to write a method to grab each name is the list and print it out as a numbered list to the terminal. By doing so I couldn’t visualize how the url was being called for each name, so to better see how things were flowing I decided to grab each url from the list of names, print out the list of urls to my terminal, and then let each individual page tell me about itself. So the second method I would write would include the name, location, year, etc. 

My first method to scrape the urls looked like this:

```
 	def self.scrape_wiki
		doc = Nokogiri::HTML(open("https://en.wikipedia.org/wiki/List_of_New_York_state_prisons"))
		links = doc.search(".div-col ul li a")
		urls = links.map{|a| "https://en.wikipedia.org#{a.attr("href")}"}
		@@all = urls.map do |url|
			self.new(url)
		end

	end
```

This method pushed a bunch of url objects into my empty `@@all` array (`@@all = [ ]` was set right after I defined my Prison class at the beginning of the class method.)

OK great! so now I had a bunch of urls printing out to my terminal, If a user selected a numbered url, I wanted it to grab each unique urls info, to do so I wrote a separate method that looked like this:


```
def self.scrape_prison_info(input)
  prison = NysPrison::Prison.find(input)
  doc = Nokogiri::HTML(open(prison.url))
     prison.name = doc.search("h1#firstHeading").text
     prison.location = doc.search("table.infobox.vcard td.label a").text
     prison.history = doc.search("p").text
   prison
end
```

Ok great! I was able to list a bunch of items and get back their attributes, but there were two problems I still had. One was I didn’t want to list the urls, I only wanted to list the name (`prison.name`) and the second problem was that` @@all` only contained the prison urls instead of all the prison attributes, making it impossible for me to call (`prison.name`)

I wanted `@@all` to contain all my prison attributes making it more versatile so i could call `prison.whatever_other_attributes_i_was_grabbing_from_anywhere_in_my_program`

I was reminded that in the Student Scraper lab we instantiated Student objects from a hash, so I decided using a hash would be the best way to push all my Prison objects into `@@all`

First I wanted to push all the values into the hash, `Prison.new(prison)`, and then my initialize method should be able to take in that hash and make @instance variables with them.

My solution was to assign a variable a hash filled with the various attribute objects I wanted to get for my prison. Inside the hash, I was calling the search method to scrape the Wikipedia page. 

In my other method that scraped the urls(`def scrape_url`), I was now calling the detail scraper method (`def scrape_details`)  from inside of the original method(`def scrape_url`). 

My new methods now looking like this:

```
	def self.scrape_wiki
		doc = Nokogiri::HTML(open("https://en.wikipedia.org/wiki/List_of_New_York_state_prisons"))
		links = doc.search(".div-col ul li a")
		urls = links.map{|a| "https://en.wikipedia.org#{a.attr("href")}"}
		@@all = urls.map do |url|
			self.scrape_prison_info(url)
		end
	end


	def self.scrape_prison_info(input)
		doc = Nokogiri::HTML(open(input))
		prison = {
			:name => doc.search("h1#firstHeading").text,
			:location => doc.search("table.infobox.vcard td.label a").text,
			:history => doc.search("p").text,
			:url => input
		}
		self.new(prison)
	end
```

These new methods fill my `@@all` array with attribute objects for each prison, enabling me to call `prison.name`, `prison.location`, etc from anywhere in my program. YAY!
