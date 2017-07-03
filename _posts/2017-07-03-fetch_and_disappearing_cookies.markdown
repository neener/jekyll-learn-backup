---
layout: post
title:  "Fetch and Disappearing Cookies"
date:   2017-07-03 20:05:56 +0000
---


While building out my jQuery Front End I was running into the problem, where a signed in user could vote on a poll, `voted_on?` was returning true, but whenever I tried to submit the voting form I was no longer signed in. My session cookies were disappearing each time I made a request using fetch. 

The fetch POST docs read :

```
fetch(url, {
  method: "POST",
  body: JSON.stringify(data),
  headers: {
    "Content-Type": "application/json"
  },
  credentials: "same-origin"
}).then(function(response) {
  response.status     //=> number 100–599
  response.statusText //=> String
  response.headers    //=> Headers
  response.url        //=> String

  return response.text()
}, function(error) {
  error.message //=> String
})
```

I was passing in the fetch headers in my submitNewPoll function like this

```
fetch('/polls', {
		method: 'POST',
		credentials: 'same-origin',
		headers: new Headers({
			'X-CSRF-Token': token,
	        		'X-Requested-With': 'XMLHttpRequest',
			'Content-Type': 'application/json',
	        		'Accept': 'application/json'
		}),
```

What fetch docs did not make clear in this POST documentation is stated clearly in this [issue]( https://github.com/github/fetch/issues/163#issuecomment-112180606) raised on the fetch github page. The most helpful contribution reads:

To have fetch send cookies back to your server, and preserve authentication information, you will need to pass the option to it every time:

```
fetch(url, {credentials: 'same-origin’})
```

What I realized after a little research is that Fetch does not use cookies by default and that you need to enable them in each of your requests.

To solve the problem of fetch ignoring the header I include this header in the response body too. I also included the header in my show function and my get index functions to maintain the user sign in once they have submitted a poll. 

What the default or implicit 'credentials' should be set to for a fetch request is a current issue in the [fetch community](https://github.com/github/fetch/pull/56#issuecomment-69113848) 
and knowing that the you had to set the credentials each time you made a request would have saved me some confusion regarding my disappearing cookies.

