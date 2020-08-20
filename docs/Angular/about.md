# Introduction

---

![Logo](/doc/Angular/introduction.PNG)



## What is Angular ?
---
Angular is a platform and framework for building single-page client applications using HTML and TypeScript. Angular is written in TypeScript. It implements core and optional functionality as a set of TypeScript libraries that you import into your apps.

The architecture of an Angular application relies on certain fundamental concepts. The basic building blocks are NgModules, which provide a compilation context for components.  

NgModules collect related code into functional sets; an Angular app is defined by a set of NgModules. An app always has at least a root module that enables bootstrapping, and typically has many more feature modules.

* Components define views, which are sets of screen elements that Angular can choose among and modify according to your program logic and data.

* Components use services, which provide specific functionality not directly related to views. Service providers can be injected into components as dependencies, making your code modular, reusable, and efficient.

Both components and services are simply classes, with decorators that mark their type and provide metadata that tells Angular how to use them.

* The metadata for a component class associates it with a template that defines a view. A template combines ordinary HTML with Angular directives and binding markup that allow Angular to modify the HTML before rendering it for display.

* The metadata for a service class provides the information Angular needs to make it available to components through dependency injection (DI).

An app's components typically define many views, arranged hierarchically. Angular provides the Router service to help you define navigation paths among views. The router provides sophisticated in-browser navigational capabilities.

For full documentation visit [angularWebsite](https://angular.io/start)

## Why choose Angular ?
---
There are several very popular JavaScript frameworks today: Angular, React, Ember, Vue...   

The other frameworks work very well, are very successful and are used on extremely well-visited sites, React and Vue in particular.   

Angular also presents a slightly higher level of difficulty, because we use TypeScript rather than pure JavaScript or the JS / HTML mixture of React.

## So what are the advantages of Angular?
---
* Angular is managed by Google - so it's unlikely to go away, and the framework development team is excellent.  

* TypeScript - this language makes development much more stable, faster and easier.  

* The Ionic framework - the framework for developing cross-platform mobile applications from a single code base - uses Angular.

Other frameworks have their advantages too, but Angular is a very relevant choice for frontend development.

TypeScript adds extremely useful features, such as, among others:

* Strict typing, which ensures that a variable or value passed to or returned by a function is of the expected type;

* So-called lambda or arrow functions, allowing a more readable code and therefore easier to maintain;

* Classes and interfaces, allowing coding in a much more modular and robust way.

