+++
date = '2026-04-20T19:04:49Z'
draft = false
title = 'JavaScript: ES6, intro to React'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

For this one, we learn from this [Modern JavaScript ](https://www.youtube.com/watch?v=NCwa_xi0Uuc).

### var, let, and const


```js
var i = 1;
let x = 1;
const y = 1;
```

- [var](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var): the variable is access-able in the function.
- [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let): the variable is access-able only is only in this block which they define.
- [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const): the variable is access-able only is only in this block which they define, *cannot be re-assigned*.

### Objects

```js
const person = {
	name: 'Moriel', // Property
	walk: function() {}, // Method
	talk() {}, // Method
};

person.talk();
person.name = '';

/* If you don't know which value we will access */
const targetMember = 'name';
person[targetMember.value] = 'Joe';
```

### this

[this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this): refers to the context where a piece of code, such as a function's body, is supposed to run. Most typically, it is used in object methods, where `this` refers to the object that the method is attached to, thus allowing the same method to be reused on different objects.
The value of `this` in JavaScript depends on how a function is invoked (runtime [binding](https://developer.mozilla.org/en-US/docs/Glossary/Binding)), not how it is defined. When a regular function is invoked as a method of an object (`obj.method()`), `this` points to that object. When invoked as a standalone function (not attached to an object: `func()`), `this` typically refers to the [global object](https://developer.mozilla.org/en-US/docs/Glossary/Global_object) (in non-strict mode) or `undefined` (in [strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)). The [`Function.prototype.bind()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) method can create a function whose `this` binding doesn't change, and methods [`Function.prototype.apply()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) and [`Function.prototype.call()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) can also set the `this` value for a particular call.

```js
const person = {
	name: 'Moriel',
	walk() { // Method
		console.log(this)l
	} 
};

person.talk();
const walk = person.walk;
walk(); // Calling the function
```
#### binding this

[bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind): method of [`Function`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function) instances creates a new function that, when called, calls this function with its `this` keyword set to the provided value, and a given sequence of arguments preceding any provided when the new function is called.

```js
const walk = person.walk.bind(person);
```

### Arrow Functions

[Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions): is a compact alternative to a traditional [function expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function), with some semantic differences and deliberate limitations in usage:

- Arrow functions don't have their own [bindings](https://developer.mozilla.org/en-US/docs/Glossary/Binding) to [`this`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this), [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments), or [`super`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/super), and should not be used as [methods](https://developer.mozilla.org/en-US/docs/Glossary/Method).
- Arrow functions cannot be used as [constructors](https://developer.mozilla.org/en-US/docs/Glossary/Constructor). Calling them with [`new`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new) throws a [`TypeError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypeError). They also don't have access to the [`new.target`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new.target) keyword.
- Arrow functions cannot use [`yield`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield) within their body and cannot be created as generator functions.

```js
// Old
const square = function(number) {
	return number * number;
}

// New
const square = number => number * number;
```

#### in Class component

```js
square = number => number * number;
```
### array.map()

[array.map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map): The **`map()`** method of [`Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) instances creates a new array populated with the results of calling a provided function on every element in the calling array.

```js
const colors = ['red', 'blue', 'yellow','green'];

const items = colors.map(color => `<li>${color}</li>`);
console.log(items);
```

### Object Destructuring

```js
const address = {
	street: '',
	city: '',
	country: '',
	building: ''
};

// Old
const street = address.street;
const city = address.city;
const country = address.country;

// New
const { street, city, country } = address;

// Can be done for only one:
const { building } = address;

// Can use an alias:
const { street: st } = address;
```

### Spread Operator

[ (. . .) ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax):The **spread (`...`)** syntax allows an iterable, such as an array or string, to be expanded in places where zero or more arguments (for function calls) or elements (for array literals) are expected. In an object literal, the spread syntax enumerates the properties of an object and adds the key-value pairs to the object being created.

```js
const first = [1, 2, 3];
const second = [4, 5, 6];

// Old
const combined = first.concat(second);

// New
const combined = ['a', ...first, 'b' ,...second];
// Can add an element inside if needed.
```

```js
const first = { Name: 'Moriel' };
const second = { Job: 'DevOps' };

const combined = {...first, ...second, Since: '2023'}
```
### Classes

[Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes): are a template for creating objects. They encapsulate data with code to work on that data. Classes in JS are built on [prototypes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain) but also have some syntax and semantics that are unique to classes.

```js
class Person {
	constructor(name) {
		this.name = name;
	}
	
	walk() {
		console.log('walk');
	}
}

const person = new Person('Moriel');
person.walk();
```

### Inheritance

[Inheritance](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain): In programming, _inheritance_ refers to passing down characteristics from a parent to a child so that a new piece of code can reuse and build upon the features of an existing one. JavaScript implements inheritance by using [objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Data_structures#objects). Each object has an internal link to another object called its _prototype_. That prototype object has a prototype of its own, and so on until an object is reached with `null` as its prototype. By definition, `null` has no prototype and acts as the final link in this **prototype chain**. It is possible to mutate any member of the prototype chain or even swap out the prototype at runtime, so concepts like [static dispatching](https://en.wikipedia.org/wiki/Static_dispatch) do not exist in JavaScript.

JavaScript is a bit confusing for developers experienced in class-based languages (like Java or C++), as it is [dynamic](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Data_structures#dynamic_and_weak_typing) and does not have static types. While this confusion is often considered to be one of JavaScript's weaknesses, the prototypal inheritance model itself is, in fact, more powerful than the classic model. It is, for example, fairly trivial to build a classic model on top of a prototypal model — which is how [classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) are implemented.

```js
class Person {
	constructor(name) {
		this.name = name;
	}
	
	walk() {
		console.log('walk');
	}
}
// the 'extends' words inheritance from the Person class
class Teacher extends Person {
	constructor(name, degree) {
		// To inherit the other constructor
		super(name);
		this.degree = degree;
	}
	
	teach() {
		console.log('teach');
	}
}
```

### Modules

[modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules): JavaScript programs started off pretty small — most of its usage in the early days was to do isolated scripting tasks, providing a bit of interactivity to your web pages where needed, so large scripts were generally not needed. Fast forward a few years and we now have complete applications being run in browsers with a lot of JavaScript, as well as JavaScript being used in other contexts ([Node.js](https://developer.mozilla.org/en-US/docs/Glossary/Node.js), for example).

Complex projects necessitate a mechanism for splitting JavaScript programs into separate modules that can be imported when needed. Node.js has had this ability for a long time, and there are a number of JavaScript libraries and frameworks that enable module usage (for example, other [CommonJS](https://en.wikipedia.org/wiki/CommonJS) and [AMD](https://github.com/amdjs/amdjs-api/blob/master/AMD.md)-based module systems like [RequireJS](https://requirejs.org/), [webpack](https://webpack.js.org/), and [Babel](https://babeljs.io/)).

`person.js`
```js
// Need to 'export' the class
export class Person {
	constructor(name) {
		this.name = name;
	}
	
	walk() {
		console.log('walk');
	}
}
```

`teacher.js`
```js
// Import the 'Person' class
import { Person } from './path/to/person';

// Need to 'export' the class
export class Teacher extends Person {
	constructor(name, degree) {
		super(name);
		this.degree = degree;
	}
	
	teach() {
		console.log('teach');
	}
}
```

`index.js`
```js
import { Teacher } from './path/to/teacher';

const teacher = new Teacher('Moriel', 'Masters');
```

### Named and Default Exports

`teacher.js`
```js
import { Person } from './path/to/person';

export function promote() {}

// It's defaulted to be expoted
export default class Teacher extends Person {
	constructor(name, degree) {
		super(name);
		this.degree = degree;
	}
	
	teach() {
		console.log('teach');
	}
}
```

`index.js`
```js
import Teacher, { promote } from './path/to/teacher';

const teacher = new Teacher('Moriel', 'Masters');
```

- Default -> `import ... from '';`
- Named -> `import { ... } from '';`

