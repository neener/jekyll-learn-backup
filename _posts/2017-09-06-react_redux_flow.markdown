---
layout: post
title:  "React Redux Flow"
date:   2017-09-06 16:36:45 +0000
---


The hardest part about familiarzing myself with React and Redux was the strucutre or destructuring of the Javascript library. I sometimes fumble over what is considered a framework or library. So to clear it up, and to have written it down (atleast as a point of reference, if i still don't have it straight) - **React** is a Javascript library for building User Interfaces. **Redux** is a Javascript library for managing application state. 

I used many different sources to reiterate the flow of how React and Redux worked together, not to mention they both have a pretty length set of new terms you have to become familiar with to even talk about what is going on.

This video helped me understand how middleware, a crucial part of Redux, was insterested into the application. [Video here](https://www.youtube.com/watch?v=AgO7YcJeBh4)

The video walks through the application flow with React and Redux:

1. The application dispatches action
2. The actions are handled by reducers
3. the reducers return a new state
4. the state is stored in the store
5. the store then passes this pack to our application

Middleware (like Redux Thunk) is passed down between our Actions and Reducers - Once the action is dispatched and right before reaching the reducer. 

For atleast a few days while introducing myself to these new terms, 'store' was one of those terms I kind of refused to totally understand, but I get it now! and its very simple. Especially reading about how some other frameworks and libraries have many 'stores'. Redux only has one store which holds your entire state tree of your application. 

From the [Redux docs](http://redux.js.org/docs/api/Store.html): 
'The only way to change the state inside it is to dispatch an action on it. A store is not a class. It's just an object with a few methods on it. To create it, pass your root reducing function to createStore.' 

Actions and Reducers are coupled nicely and only take a few rounds to understand how they interact and then affect the View/App. 

Say we wants to to CRUD some items, our reducers would handle this. We would need to give it an default state of an empty array. Then it would hand adding items, updating and item, and removing an item. You can think of your reducers as a controller (in Rails). 

An action is an object that returns a type and an item. In action you use all your services to dispatch. Then you use all your actions inside of your components. 

So if you have something like 'SET_ITEM' in your reducer, it is going to find the match in your actions and run through the dispatch method in your actions.

Another hurdle was routing. Redux provides a tools called react-router-redux. This [blog](https://blog.marvelapp.com/managing-the-url-in-a-redux-app/) took me through the steps of how to use the router. 

'With react-router-redux the location state is kept in your redux store as plain object. This means you can inject location information with connect as you would any other state. The LOCATION_CHANGE action is dispatched when the URL changes so you can listen for this in your reducers.
'
React-router-redux gives you host of methods to use for routing, like Link, Browser Router, Provider, Route, and gives you acces to 'history'.

This confused me for a while, but it turns out that is just a poorly named library. 

"Dan Abramov noted this library might be better called redux-history as it does not require React Router. It simply allows you to keep the location state in your redux store. Additionally it provides utilities for integrating with React Router."

Last, I would like to touch on Stateless and Stateful Components. 

When you see a stateless function component, you know it's simply a function that takes props and spits out HTML. This [blogpost](https://hackernoon.com/react-stateless-functional-components-nine-wins-you-might-have-overlooked-997b0d933dbc) lays out the benefits of stateless components.

"Stateless functional components are useful for dumb/presentational components. Presentational components focus on the UI rather than behavior, so it’s important to avoid using state in presentational components. Instead, state should be managed by higher-level “container” components, or via Flux/Redux/etc. Stateless functional components don’t support state or lifecycle methods. This is a good thing. Why? Because it protects from laziness."

Stateless components force you to keep your component pure and put state management in higher level container components.

You want the parent component (smart componenent) to have access to the store and the children components (dumb components).

I found the Learn curriculm to be a good overview of React and Redux, but really i don't think I would have had a full understanding if i hadn't reviewed the [egghead videos](https://egghead.io/) or watch Dan Abramov's [youtube lectures](https://www.youtube.com/watch?v=xsSnOQynTHs&t=2s)






