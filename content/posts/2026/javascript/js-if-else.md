+++
date = '2026-04-15T16:45:24Z'
draft = false
title = 'JavaScript: "if" and "else" statements'
author = 'Moriel Mauni'
tags = ["JavaScript"]
categories = ["Code"]
description = " "
+++

### if and else

[if()]([https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/if](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else)): The **`if...else`** statement executes a statement if a specified condition is [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy). If the condition is [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy), another statement in the optional `else` clause will be executed.

```js
if (something === something) { // To check equlity in data types use "==="
 do();
}   else {
 doThat();
}
```

```js
if (something > something) { // <
 do();
}   else {
 doThat();
}
```

### Combining comparators

- **&&**: AND.
- **||**: OR.
- **!**:  NOT.

```js
if (something > 70 && something <= 10) {
 do();
}
if (something != 2) {
 do();
}
}   else {
 doThat();
}
```

#### Challenge

**FizzBuzz** is a classic programming challenge and children's game where counting numbers are replaced with "Fizz" for multiples of three, "Buzz" for multiples of five, and "FizzBuzz" for multiples of both 3 and 5 (15).

```js
var output = [];
var count = 1;

function fizzBuzz() {
 // If (X%3=0) and (X%5=0) then FizzBuzz
 if (count % 3 === 0 && count % 5 === 0) {
  output.push("FizzBuzz");
 }
 // If [X%3=0] then Fizz
 else if (count % 3 === 0) {
  output.push("Fizz");
 }
 // If [X%5=0] then Buzz
 else if (count % 5 === 0) {
  output.push("Buzz");
 }
 // Else count
 else {
  output.push(count);
 }

 count++;
    console.log(output);
}
```
