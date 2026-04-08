+++
date = '2026-04-08T19:01:06Z'
draft = false
title = 'JavaScript: Variables'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Prompt a message

[prompt](https://developer.mozilla.org/en-US/docs/Web/API/Window/prompt): instructs the browser to display a dialog with an optional message prompting the user to input some text, and to wait until the user either submits the text or cancels the dialog.

```js
prompt("message");
```

To save the information inside a variable:

```js
var variableName = variableValue;
var variableName = "variableValue";
```

Save user input inside a variable

```js
var yourName = prompt("message");
```

Write strings with variables

```js
alert("Hello " + yourName + ", My name is " + myName);
```

#### Challenge

Switch variables `a`and `b`:

My solution:

```js
function test() {
 var a = "3";
 var b = "8";
 
 //
 var c = "3";
  var a = "8";
  var b = c;
 //
 
 console.log("a is" + a);
 console.log("b is" + b);
}
```

Teacher solution:

```js
function test() {
 var a = "3";
 var b = "8";
 
 //
 var c = a;
  a = b;
  b = c;
 //
 
 console.log("a is" + a);
 console.log("b is" + b);
}
```
