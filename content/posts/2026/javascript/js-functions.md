+++
date = '2026-04-14T09:44:42Z'
draft = false
title = 'Js Functions'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### First things first:
Sorry for 2 days without uploading, I have no internet due to the provider being an idiot.

### Functions 

[Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions): Functions are one of the fundamental building blocks in JavaScript. A function in JavaScript is similar to a procedure—a set of statements that performs a task or calculates a value, but for a procedure to qualify as a function, it should take some input and return an output where there is some obvious relationship between the input and the output. To use a function, you must define it somewhere in the scope from which you wish to call it.

```js
function name() {
	do;
	do;
}

// Call the function
name();
```

#### Challenge

https://stanford.edu/~cpiech/karel/ide.html

Help Karel to go to top right: 15x15
```js
/**
 * Welcome to the Stanford Karel IDE.
 * This is a free space for you to 
 * write any Karel program you want.
 **/
function main(){
   //your code here
   move14();
   turnLeft();
   move14();
}

function move14() {
   move();
   move();
   move();
   move();
   move();
   move();
   move();
   move();
   move();
   move();
   move();
   move();
   move();
   move();
   }
```

Make a diagonal beeper

```js
/**
 * Welcome to the Stanford Karel IDE.
 * This is a free space for you to 
 * write any Karel program you want.
 **/
function main(){
   //your code here
   putBeeper();
   toTheSpot();
   toTheSpot();
   toTheSpot();
   toTheSpot();
}

function toTheSpot() {
   move();
   turnLeft();
   move();
   putBeeper();
   left3Times();
   }
   
function left3Times(){
   turnLeft();
   turnLeft();
   turnLeft();
   }
```

Have Karel to make a chessboard:

```js
/**
 * Welcome to the Stanford Karel IDE.
 * This is a free space for you to 
 * write any Karel program you want.
 **/
function main(){
   //your code here
   left2Right();
   left2Right();
   turnUpRight();
   right2Left();
   right2Left();
   turnUpLeft();
   left2Right();
   left2Right();
   turnUpRight();
   right2Left();
   right2Left();
   turnUpLeft();
   left2Right();
   left2Right();
   putBeeper();
}

function left2Right() {
   putBeeper();
   move();
   move();
   }

function turnUpRight() {
   putBeeper();
   turnLeft();
   move();
   turnLeft();
   }
   
function right2Left() {
   move();
   putBeeper();
   move();
   }

function turnUpLeft() {
   turnRight();
   move();
   turnRight();
   }
```

### Parameters and Arguments

```js
function exampleFunction(argument) {
	var cost = argument;
	// do something with cost
}

exampleFunction(argument);
```

#### Challenge

Create a function that buy bottles from argument based on money it have.
```js
var startMoney = 10
var bottleCost = 1.5

function buyBottles(bottles) {
	var leftMoney = startMoney - (bottles * bottleCost);
	alert("You bought " + bottles + " bottles, and have " + leftMoney + "$ left");
}

buyBottles(3);
```

In this challenge, you are going to create a function that tells us how many days, weeks and months we have left if we live until 90 years old.
```js
function lifeInWeeks(age) {
    
/************Don't change the code above************/    
    
    //Write your code here.
    var monthsLeft = (90 - age) * 12;
    var weeksLeft = (90 - age) * 52;
    var daysLeft = (90 - age) * 365;
    
    console.log("You have " + daysLeft + " days, " + weeksLeft + " weeks, and " + monthsLeft + " months lefts.")
    
    
/*************Don't change the code below**********/
}
```

### Outputs and return value

```js
function exampleFunction(number) {
	return number + 1;
}

var newNumber = exampleFunction(number);
```

#### Challenge

Make a BMI calculator.
```
         Weight(kg)
BMI =  --------------
        Height^2(m^2)
```

```js
//Create your function below this line.
//The first parameter should be the weight and the second should be the height.
function bmiCalculator(weight, height) {
    var bmi = weight / (height * height);
    return Math.round(bmi)
}


/* If my weight is 65Kg and my height is 1.8m, I should be able to call your function like this:

var bmi = bmiCalculator(65, 1.8); 

bmi should equal 20 when it's rounded to the nearest whole number.

*/

```

Other options
```js
function bmiCalculator(weight, height) {
    var bmi = weight / (Math.pow(height, 2));
    return Math.round(bmi)
}
```
