+++
date = '2026-04-09T18:06:49Z'
draft = false
title = 'JavaScript: Naming conventions and Strings'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Naming and Naming conventions

- Give variables meaning-full names: `myName`, `inputAge`, `userPick`.
- Variables can't start with numbers, but can contains numbers.
- Can't have space inside.
- Only letters, numbers, `$`, and `_`.

### Strings

- You can combine strings with `+`:
```js
var a = "Hello";
var b = "World";
alert("a" + " " + "b"); // Hello World
alert("a" + " b"); // Hello World
alert("a " + "b"); // Hello World
```


#### Challenge 

Create a greeting Alert using 2 variables: message and name.

```js
var name = prompt("What is your name?");
var message = "Hello there ";
alert(message + name);
```

### String Lengths

[length](https://developer.mozilla.org/en-US/docs/Web/API/NamedNodeMap/length): The read-only **`length`** property of the [`NamedNodeMap`](https://developer.mozilla.org/en-US/docs/Web/API/NamedNodeMap) interface is the number of objects stored in the map.

```js
var name = "Moriel";
name.length;
```

#### Challenge 

Give a prompt, tell how many characters are, and how many left to 140.

My solution: 
```js
var userMessage = prompt("Insert your message: ");
var messageLength = userMessage.length;
var leftLength = 140 - messageLength;
alert("You used " + messageLength + " characters, you left with " + leftLength + " characters");
```

Teacher:
```js
var userMessage = prompt("Insert your message: ");
var messageLength = userMessage.length;
alert("You used " + messageLength + " characters, you left with " + (140 - messageLength) + " characters");
```

### Slicing and extracting parts of a string

[slice(x,y)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice): The **`slice()`** method of [`Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) instances returns a [shallow copy](https://developer.mozilla.org/en-US/docs/Glossary/Shallow_copy) of a portion of an array into a new array object selected from `start` to `end` (`end` not included) where `start` and `end` represent the index of items in that array. The original array will not be modified.

Example
```js
var name = "Moriel";
name.slice(0,1); // From position 0 to 1 not included.
// prints: "M"
name.slice(0,3); // From position 0 to 3 not included.
// prints: "Mor"
```


#### Challenge

Cut up the message above 140 characters.

```js
var userMessage = prompt("Insert your message: ");
alert(userMessage.slice(0,140));
```

Can be done in one line:
```js
alert(prompt("Insert message:").slice(0,140));
```

### toUpperCase / toLowerCase

[toUpperCase()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toUpperCase): The **`toUpperCase()`** method of [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) values returns this string converted to uppercase.

```js
var name = "Moriel";
name.toUpperCase();
// prints: "MORIEL"
// To keep the new upper case as variable:
name = name.toUpperCase();
```

[toLowerCase()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase): The **`toLowerCase()`** method of [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) values returns this string converted to lower case.

```js
var name = "Moriel";
name.toLowerCase();
// prints: "moriel"
// To keep the new upper case as variable:
name = name.toLowerCase();
```

#### Challenge

Ask a prompt for a name, be able to alert "Hello, Name"

```js
var userName = prompt("What is your name?");
var cap0 = userName.slice(0,1);
var restOfName = userName.slice(1);
alert(cap0.toUpperCase() + restOfName.toLowerCase());
```
