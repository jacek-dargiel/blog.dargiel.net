---
title: "Angular Restmod: the better alternative to ng-resource"
date: 2014-09-19 22:00
tags:
- JavaScript
- AngularJS
---
When building enterprise web apps it's important to make some good design choices right in the beginning of the development process. One of those things is designing how your client application will communicate with your backend server. `$http` calls are simple to use, but when your application gets bigger, you will find it unwieldy. This is where [REST APIs][w:rest] come in. 

In AngularJS, [ng-resource][] is the build-in way to communicate with REST APIs. If you haven't tried it yet, I suggest you give it a shot. Working with RESTfull resources requires a different approach, but it's well worth getting used to. The AngularJS Tutorial has [a step on how to use ng-resource][ngr-tutorial]. This however is not the only module out there that lets you interact with your REST API. I have worked with these:

* [ng-resource][]
* [Restangular][]
* [Angular Rails Resource][ng-rails-res]

Recently however, I've found a new player - [Angular Restmod][restmod]. They've reached a stable 1.0 release number just a few days ago, and I've started using it in a project I'm working on for one of our clients.

Restmod tries to reach maximum compatibility with the [JSON API standard][json-api], so it can extract your resources from specific JSON roots, read metadata from the `meta` root and use linked resources. This approach is working quite well for me thus far. 

<!-- more -->

To interact with an API, you need to define a restmod model:

```javascript
module.factory('Bike', function(restmod) {
    return restmod.model('/bikes');
});
```
And from now on you can inject the resource wherever you need it. All actions are defined on the objects prototype, so you can `GET` your resource using `Bike.$find(1)`. This will issue a `GET` request to `/bikes/1` and will fill your objects with the data returned from the server. Restmod also reveals the `$then()` method, so you can do something when a response arrives:

```javascript
Bike.$find(1)
Bike.$then(function(){
	if(Bike.brand === 'giant'){
		Bike.brand = 'Giant'
		Bike.$save();
	}
});
```

If you need a real promise for example to resolve a route, you can use the `$asPromise()` method to get one.

Restmod seriously shines with its support for model relations. You can define these kinds of relations:

* `HasOne`
* `HasMeny`
* `BelongsTo`
* `BelongsToMeny`

With relations you can don't have to use placeholder resources, you can do things like this:

```javascript
// Define a User resource:
module.factory('User', function(restmod) {
	return restmod.model('/users');
});
// Define a Bike resource which relates to the User:
modeule.factory('Bike', function(restmod){
	return restmod.model('/bikes').mix({
		owner: {
			belongsTo: 'User', 
			key: 'last_owner_id'
		}
	});
});
```

Now, when you use `Bike.$find(1)`, and your API responds with something similar to this:

```
{
	"id": 1,
	"brand": "giant",
	"last_owner_id": 42
}
```

Then you can get the User which owns the bike with a simple relation call: `Bike.owner.$fetch()`, which will issue a `GET` to `/users/42`.

There's a lot more you can do with Restmod. Be sure to read the [readme][RM-readme] and the [API integration FAQ][RM-api-faq]. If you're looking for an ORM *better then ng-resource*, then Restmod is definitely worth trying out.


[w:rest]: 			https://en.wikipedia.org/wiki/Representational_state_transfer
[ng-resource]: 		https://docs.angularjs.org/api/ngResource/service/$resource
[ngr-tutorial]: 	https://docs.angularjs.org/tutorial/step_11
[Restangular]: 		https://github.com/mgonto/restangular
[ng-rails-res]: 	https://github.com/FineLinePrototyping/angularjs-rails-resource
[restmod]: 			https://github.com/platanus/angular-restmod
[json-api]: 		http://jsonapi.org/
[RM-readme]: 		https://github.com/platanus/angular-restmod/blob/master/README.md
[RM-api-faq]: 		https://github.com/platanus/angular-restmod/blob/master/docs/guides/integration.md