+++
date = '2026-04-16T19:22:45Z'
draft = false
title = 'JavaScript: Arrays'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Array []

[Arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array): The **`Array`** object, as with arrays in other programming languages, enables [storing a collection of multiple items under a single variable name](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/Arrays), and has members for [performing common array operations](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#examples).

```js
var array = [];
```

```js
var strings = [string1, string2, string3, string4, string5];
console.log(strings[1]); // return string2
console.log(string.length); // print 5
console.log(string.includes(string2)); // Return bollean if is in array
```

#### Challenge

You are going to write a function which will select a random name from a list of names. The person selected will have to pay for everybody's food bill.
```js
var names = ["Angela", "Ben", "Jenny", "Michael", "Chloe"];

function whosPaying(names) {
    
/******Don't change the code above*******/
    
    //Write your code here.
    var randomNumber = Math.floor(Math.random() * names.length);
    return names[randomNumber] + " is going to buy lunch today!";
/******Don't change the code below*******/    
}
```

