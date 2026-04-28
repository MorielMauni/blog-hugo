+++
date = '2026-04-28T18:44:32Z'
draft = false
title = 'React: Event Handling'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Event Handling

### onClick

#### Function component

`/src/component/FunctionClick.js`
```js
import React from "react"

// `rfce` snipet
function FunctionClick() {

    // function for the event handler
    const clickHandler = () => console.log("Buttom clicked");

    return (
    <div>
        {/* We don't want to call function with () = function call, it's only              a function  */}
        <button onClick={clickHandler}>Click</button>
    </div>
  )
}

export default FunctionClick
```

#### Class component

```js
import React, { Component } from 'react'

// `rce` 
class ClassClick extends Component {
  
    clickHandler = () => console.log("Buttom clicked");

  
    render() {
    return (
      <div>
        <button onClick={this.clickHandler}>Click</button>
      </div>
    )
  }
}

export default ClassClick
```
