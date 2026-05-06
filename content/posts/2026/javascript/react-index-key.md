+++
date = '2026-05-06T11:48:42Z'
draft = false
title = 'React Index Key'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Index Key

Use the index of array as a `key`

```js
import React from 'react'


function NameList() {
    const names = ['Moriel', 'Eliya', 'Mazal', 'Moriel']

    const nameList = names.map((name, index) => <h2 key={index}>{name} and {index}</h2>)
    return <div>{nameList}</div>
}

export default NameList
```

#### When to use index as a key?

- The items in your list do not have a unique id.
- The list is static and will not change.
- The list will never be re-ordered or filtered.
