+++
date = '2026-04-18T18:15:24Z'
draft = false
title = 'JavaScript: for Loops'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### For

[for()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for): The **`for`** statement creates a loop that consists of three optional expressions, enclosed in parentheses and separated by semicolons, followed by a statement (usually a [block statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/block)) to be executed in the loop.

```js
for (startingPoint; defindEndPoint; whatIsTheChange){
	// Do something
}
```

Example one:
```js
for (i=0; i<2; i++){
	// Do something
}
// prints: 0 1
```

Example Two:
```js
for (var i = 1; i<=10; i++) {
	console.log(i);
}
// prints: 1 2 3 4 5 6 7 8 9 10
```

#### Challenge

**FizzBuzz** is a classic programming challenge and children's game where counting numbers are replaced with "Fizz" for multiples of three, "Buzz" for multiples of five, and "FizzBuzz" for multiples of both 3 and 5 (15).

```js
var output = [];

function FizzBuzz() {
    
    for (var count = 1; count <= 100; count++) {
        
        if (count % 3 === 0 && count % 5 === 0) {
            output.push("FizzBuzz");
        } else if (count % 3 === 0) {
            output.push("Fizz");
        } else if (count % 5 === 0) {
            output.push("Buzz");
        } else {
            output.push(count);
        }
        
    }
    console.log(output);
}
```
