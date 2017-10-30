---
layout: post
title:      "Customizing Shopify Themes"
date:       2017-10-29 23:00:39 -0400
permalink:  customizing_shopify_themes
---


This week I brushed up on my Shopify skillset. I was given a code challenge to recreate this site's homepage and one collection page: https://staud.clothing/ 

It was a lesson heavy in html, css, and JavaScript.

I haven’t done much styling since I started the Flatiron course so there was alot of new css challenges I faced. 

A couple problems and how I solved them are below.

* adding a ‘back to top botton’ - 

[using scroll](https://developer.mozilla.org/en-US/docs/Web/API/Window/scroll
)

I solved this by adding a 

```
<div id="top">BACK TO TOP</div>
```

in the collection.liquid template

and added jQuery in the theme.js file

```
$("#top").on("click", function(){
	window.scroll(0, 0)
})
```

* I had to turn off a lot of hover effects and add some new ones

[css hover effects](https://www.w3schools.com/cssref/sel_hover.asp)

* I used the [jQuery attr](http://api.jquery.com/attr/) method to allow for images to change when you rollover them.

```
$(".grid-view-item__image").on("mouseover", function(){
        console.log("HELLO")
      	var img = $(this);
        console.log(img);
        var altSrc = img.attr("data-alt-src");
        console.log("ALT SRC", altSrc);
        var src = img.attr("src");
        if(altSrc && altSrc !== ""){
        	img.attr("src", altSrc);
        	img.attr("data-alt-src", src);
        }
      });

      $(".grid-view-item__image").on("mouseout", function(){
      	var img= $(this);
        var altSrc = img.attr("data-alt-src");
        var src = img.attr("src");
        if(altSrc && altSrc !== ""){
        	img.attr("src", altSrc);
        	img.attr("data-alt-src", src);
        }
      });

```

* For the email section I needed to use [jQuery’s focus and blur](https://stackoverflow.com/questions/2212583/affecting-parent-element-of-focusd-element-pure-csshtml-preferred) to make the text move when someone clicked on the input

```
jQuery("#emailinput").on("focus", function(){
      	jQuery("#emaillabel").addClass("active")
      })
      jQuery("#emailinput").on("blur", function(){
      	jQuery("#emaillabel").removeClass("active")
      })

```

* remove border on an input text element

[remove border highlight](https://stackoverflow.com/questions/1457849/how-to-remove-the-border-highlight-on-an-input-text-element)

*  add transitions to a button for when you roll over it

[css transitions](https://www.w3schools.com/css/css3_transitions.asp)

* added font-awesome assets to shopify so I could use their search icon

[fontawesome](https://github.com/nairdairda/Shopify-Font-Awesome)

*  had to deal with creating model boxes using [box-sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)

*  added pointer events and deleted some [pointer events](https://developer.mozilla.org/en-US/docs/Web/CSS/pointer-events)
*  added a svg image as a logo using [custom settings](https://help.shopify.com/themes/liquid/objects#global-objects)
to get the svg from the example site i used [svg-crowbar](http://nytimes.github.io/svg-crowbar/)

which is a bookmark tool thats allows you to grab any svg image from any website. cool!

I am excited to be exploring how Shopify is organized and familiarize myself with custom templating.


