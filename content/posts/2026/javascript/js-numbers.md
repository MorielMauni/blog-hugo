+++
date = '2026-04-11T19:26:08Z'
draft = false
title = 'JavaScript: Numbers'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Types of numbers

```js
var a = 3 + 3;
var a = 3 - 3;
var a = 3 * 3;
var a = 3 / 3;
var a = 9 % 6; // Gives the reaming 3
```

#### Challenge

Dog age to human age: Ask the age of the dog and prompt the age of a human.
```js
humanAge = (dogAge -2) * 4 + 21
```

```js
var dogAge = prompt("What is your dog's age?");
alert("Your dog's human age is " + ((dogAge -2) * 4 + 21));
```

### Increment and Decrement

[Increment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Increment): The **increment (`++`)** operator increments (adds one to) its operand and returns the value before or after the increment, depending on where the operator is placed.

[Decrement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Decrement): The **decrement (`--`)** operator decrements (subtracts one from) its operand and returns the value before or after the decrement, depending on where the operator is placed.

```js
var x = 5;
x = x + 1; // 6
x++; // 6
x--; // 4
x += 2; // 7
x +- 2 // 3
var y = 3;
x += y; //8
```
