+++
date = '2026-05-03T18:01:03Z'
draft = false
title = 'React: List Rendering'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## List Rendering

[List Rendering](https://react.dev/learn/rendering-lists): You will often want to display multiple similar components from a collection of data. You can use the [JavaScript array methods](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array#) to manipulate an array of data. On this page, you’ll use [`filter()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) and [`map()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/map) with React to filter and transform your array of data into an array of components.

[map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map): method of [`Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) instances creates a new array populated with the results of calling a provided function on every element in the calling array.

```js
const array = [1, 4, 9, 16];

// Pass a function to map
const mapped = array.map((x) => x * 2);

console.log(mapped);
// Expected output: Array [2, 8, 18, 32]
```

### Example

```js
import React from 'react'

function NameList() {
    
    const names = ['Moriel', 'Eliya', 'Mazal']
  
    return (
    <div>
        {
            names.map(name => <h2>{name}</h2>)
        }
    </div>
  )
}

export default NameList
```

```js
import React from 'react'

function NameList() {
    
    const persons = [
        {
            id: 1,
            name: 'Moriel',
            age: 30,
            skiils: 'ya know'
        },

        {
            id: 2,
            name: 'Mazal',
            age: 27,
            skiils: 'ya know'
        },
        
        {
            id: 3,
            name: 'Eliya',
            age: 1,
            skiils: 'ya know'
        },
        
        {
            id: 4,
            name: 'Isikit',
            age: 0,
            skiils: 'ya know'
        },
        
    ]

    const personList = persons.map(person => (
        <h2>
            I am {person.name}, and I {person.age} years old. My skills are: {person.skiils}.
        </h2>
    ))
    return <div>{personList}</div>
}

export default NameList
```

#### Refactor

1 render the list

```js
import React from 'react'
import Person from './Person'

function NameList() {
    
    const persons = [
        {
            id: 1,
            name: 'Moriel',
            age: 30,
            skiils: 'ya know'
        },
    ]

    const personList = persons.map(person => (<Person person={person} />))
    return <div>{personList}</div>
}

export default NameList
```

1 render the HTML:

```js
import React from 'react'

function Person({person}) {
  return (
    <div>
        <h2>
            I am {person.name}, and I {person.age} years old. My skills are: {person.skiils}.
        </h2>
    </div>
  )
}

export default Person
```
