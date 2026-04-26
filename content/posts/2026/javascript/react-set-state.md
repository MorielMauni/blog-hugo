+++
date = '2026-04-26T18:11:21Z'
draft = false
title = 'React: Set State'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Set State 

setState: in React, `setState` is the mechanism used to update a component's state, triggering a re-render of the user interface. Its implementation differs depending on whether you are using **Function Components** (modern standard) or **Class Components**.

```js
this.setState({
	this.state.X + 1
})
```

`/src/component/Counter.js`
```js
import React, { Component } from 'react'

// `rce` snippet
class Counter extends Component {

    // `rconst` snippet
    constructor(props) {
      super(props)
      this.state = {
         count: 0
      }
    }

    increment() {
        // Not `this.state`, never changed state directly
        this.setState({
            count: this.state.count + 1
        },
        () => {console.log('Callback value', this.state.count)})
        console.log(this.state.count)
    }

  render() {
    return (
      <div>
        <div>Count - {this.state.count}</div>
        <button onClick={() => this.increment()}>Increment</button>
      </div>
    )
  }
}

export default Counter
```

### Multiple setState calls

React group multiple `setState` calls into one for performance, they are done in 1 go.
If need to updated the state, based on the previous one, you need to pass it in a function.

```js
    increment() {
        this.setState((prevState) => ({
            count: prevState.count + 1
        }))
        console.log(this.state.count)
    }
    
    // This is changing the state 5 times 
    incrementByFive() {
        this.increment()
        this.increment()
        this.increment()
        this.increment()
        this.increment()
    }
    
  render() {
    return (
      <div>
        <div>Count - {this.state.count}</div>
        <button onClick={() => this.incrementByFive()}>Increment</button>
      </div>
    )
  }
}
```

Gemini explanation: 
```
### 1. The Functional `setState`: `(prevState) => ({ ... })`

Instead of passing an object directly into `setState` (like `{ count: this.state.count + 1 }`), you are passing a function. This is critical because of your `incrementByFive()` method.

- **The Problem with Objects:** React groups (or "batches") multiple `setState` calls together into a single update for better performance. If you called `incrementByFive()` using the object method, React would read `this.state.count` as `0` for all five calls simultaneously. It would essentially execute `0 + 1` five times, and your final count would only be `1`.
    
- **The Solution with Functions:** By passing a function with the `prevState` argument, you are telling React, _"Don't look at what the state is right now on the screen. Look at what the state will be after the previous updates in the queue have finished."_ * Call 1 gets a `prevState` of 0 and returns 1.
    
    - Call 2 gets a `prevState` of 1 and returns 2.
        
    - ...and so on, accurately incrementing the count to 5.
        

### 2. The Asynchronous `console.log(this.state.count)`

You might notice that when you click the button, this `console.log` prints a number that is one step behind what you actually see on the screen.

- **Why it happens:** `this.setState` is an **asynchronous** operation. When you call it, React registers the request to change the state, but it doesn't pause the rest of your code to wait for the update to finish. It immediately moves to the next line—your `console.log`.
    
- **The Result:** Because the state hasn't officially updated yet when the `console.log` fires, `this.state.count` still holds the _old_ value.
```
