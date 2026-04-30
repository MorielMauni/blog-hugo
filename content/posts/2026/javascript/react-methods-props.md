+++
date = '2026-04-30T21:14:54Z'
draft = false
title = 'React: Methods as Props'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Methods as Props

In React, "**methods as props**" is a pattern used to allow child components to communicate with their parents. While data typically flows downward from parent to child, [passing functions](https://react.dev/learn/passing-props-to-a-component) enables the child to trigger actions in the parent, such as [updating state](https://dev.to/vadims4/passing-down-functions-in-react-4618).

`ParentComponent.js`

```js
// `rce`
import React, { Component } from 'react'
import ChildComponent from './ChildComponent';

class ParentComponent extends Component {
    // rconst
    constructor(props) {
      super(props)
    
      this.state = {
         parentName: 'Parent'
      }

      this.greetParent = this.greetParent.bind(this);
    }
    
    // method
    greetParent = () => alert(`Hello ${this.state.parentName}`);
  
    render() {
    return (
      <div>
        {/* Parent rendering the child, adding an attribute, assigned a method to it  */}
        <ChildComponent greetHandler={this.greetParent} />
      </div>
    )
  }
}

export default ParentComponent
```

`ChildComponent.js`

```js
import React from 'react'

// rfce
function ChildComponent(props) {
  return (
    <div>
        <button onClick={props.greetHandler}>Greet Parent</button>
    </div>
  )
}

export default ChildComponent
```

Arrow function is the easy way to call from Child to Parent:

```js
<button onClick={() => props.greetHandler('Child')}>Greet Parent</button>
```
