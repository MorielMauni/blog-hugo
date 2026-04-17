+++
date = '2026-04-17T09:32:53Z'
draft = false
title = 'JavaScript: While loops'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### While

[while](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/while): The **`while`** statement creates a loop that executes a specified statement as long as the test condition evaluates to true. The condition is evaluated before executing the statement.

```js
while (somthing is true) {
	// Do somthing
}
```

Example:
```js
var i = 1;
while (i <= 10) {
	console.log(i);
	i++
}

// Prints "1 2 3 4 5 6 7 8 9 10" and exits.
```

#### Challenge

Print the words of "99 bottles of beer on the wall"
```
99 bottles of beer on the wall, 99 bottles of beer.  
Take one down and pass it around, 98 bottles of beer on the wall.
```

```js
// Starting point, 99
var i = 99;
// while i is bigger or is 0
while (i > 0) {
	// print first part with bigger number
	console.log(i + " bottles of beer on the wall, " + i + " bottles of beer.");
	i-- // reduce number by 1
	if (i === 0) {
	    console.log("No more bottles of beer on the wall, no more bottles of beer. \nGo to the store and buy some more, 99 bottles of beer on the wall.");
	if (i === 1) {
	    console.log("1 bottle of beer on the wall, 1 bottle of beer. \nTake one down and pass it around, no more bottles of beer on the wall.");
	}
	} else {
	// print the 2nd half
    console.log("Take one down and pass it around. " + i + " bottles of beer on the wall.");
	}

}
```

