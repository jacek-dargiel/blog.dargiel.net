---
title: How to open a modal from any state using UI-Router
date: 2014-10-09 22:30
tags: 
- JavaScript
- AngularJS
---
This post is a follow-up to a question that was asked on the [AngularJS Poland][] Facebook group. One of the members wanted to open a modal window from anywhere in the app using [UI-Router][]. At first this seemed simple enough. This is a common problem -- so common that it has a section in the [UI-Router FAQ][]. 

### Using a modal child state

The most simplified example goes like this:

{% iframe //embed.plnkr.co/yqcT6DY6KD5A94fSvGX9/app.js %}

In the above plunker we have two states defined -- a parent state for the view (`index`) and a child state for the modal (`index.terms`). The child state doesn't have a template nor controller as this isn't an actually usable state. The magic happens because UI-Router executes the `onEnter` callback. This callback has the dependency injection system build in, and can open the modal dialog using the `$modal` service.

This works fine, unfortunately, this didn't solve the initial problem -- opening the modal window from anywhere in the app. In the above example, the modal state is a child of the `index` state, and opening it from any state other than `index` will cause UI-Router to switch to `index`. To solve this problem we need to take a different approach.

### Using the `$stateChangeStart` event

{% iframe //embed.plnkr.co/6dZfgb/app.js %}

This approach involves defining a placeholder state for the modal (`terms` in the above plunker) which is a top-level state. Next we need to listen to the `$stateChangeStart` event. UI-Router broadcasts this event from the `$rootScope` using [Angulars event system][] every time a state is about to change. Among the arguments passed to the event listeners this event passes the `toState` object. We can use this to check which state is to be displayed, display the modal dialog using the `$modal` service, and cancel the state transition using `event.preventDefault()`. I would recommend listening to `$stateChangeStart` in the `run` block of the module -- this is far more elegant than using an application-wide controller encapsulating the entire app.

The above example solves the problem of displaying the modal from anywhere in the application. Some users expressed that this solution has some down sides, like unnaturally separating the modal display logic from the router configuration block. I couldn't think of an elegant solution to this problem. If you see one, drop a line in the comments.

[AngularJS Poland]: 		https://www.facebook.com/groups/angularjs.polska/
[UI-Router]: 				https://github.com/angular-ui/ui-router
[UI-Router FAQ]: 			https://github.com/angular-ui/ui-router/wiki/Frequently-Asked-Questions#how-to-open-a-dialogmodal-at-a-certain-state
[Angulars event system]:	https://docs.angularjs.org/guide/scope#scope-events-propagation