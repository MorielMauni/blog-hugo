+++
date = '2026-04-29T19:20:53Z'
draft = false
title = 'React: Binding Events'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Bind 
We bind because of `this` keyword in JS.

### Bind

`/src/component/EventBind.js`
```js
import React, { Component } from 'react'

class EventBing extends Component {

    // rconst 
    constructor(props) {
      super(props)
    
      this.state = {
         message: 'Hello'
      }
    }
    
    clickHandler () {
        this.setState({
            message: "goodbye"
        })
    }

  render() {
    return (
      <div> 
        <div>{this.state.message}</div>
        {/* Adding `.bind(this)` */}
        <button onClick={this.clickHandler.bind(this)}>Clicks</button>
      </div>
    )
  }
}

export default EventBing
```


### Arrow function

Most easy way

```js
import React, { Component } from 'react'

class EventBing extends Component {

    // rconst 
    constructor(props) {
      super(props)
    
      this.state = {
         message: 'Hello'
      }
    }

    clickHandler = () => this.setState({message: "goodbye!!"})

  render() {
    return (
      <div> 
        <div>{this.state.message}</div>
        {/* Adding `.bind(this)` */}
        <button onClick={() => this.clickHandler()}>Clicks</button>
      </div>
    )
  }
}

export default EventBing
```

### Bind in constructor

React recommended 

```js
import React, { Component } from 'react'

class EventBing extends Component {

    // rconst 
    constructor(props) {
      super(props)
    
      this.state = {
         message: 'Hello'
      }

      this.clickHandler = this.clickHandler.bind(this)

    }
    
    clickHandler () {
        this.setState({
            message: "goodbye"
        })
    }

  render() {
    return (
      <div> 
        <div>{this.state.message}</div>
        {/* Adding `.bind(this)` */}
        <button onClick={this.clickHandler}>Clicks</button>
      </div>
    )
  }
}

export default EventBing
```

### Arrow as class property

React recommended 

```js
import React, { Component } from 'react'

class EventBing extends Component {

    // rconst 
    constructor(props) {
      super(props)
    
      this.state = {
         message: 'Hello'
      }
    }
    
    clickHandler = () => {
        this.setState({
            message: "goodbye"
        })
    }

  render() {
    return (
      <div> 
        <div>{this.state.message}</div>
        {/* Adding `.bind(this)` */}
        <button onClick={this.clickHandler}>Clicks</button>
      </div>
    )
  }
}

export default EventBing
```
