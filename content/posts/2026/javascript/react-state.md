+++
date = '2026-04-25T18:54:33Z'
draft = false
title = 'React: State'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## State


### Props vs State

| Props                       | State                                   |
| --------------------------- | --------------------------------------- |
| Get passed to the component | Managed within the component            |
| Function  parameters        | Variables declared in the function body |
| Immutable                   | Can be changed                          |
| Functional: `props.`        | Functional: `useState` Hook             |
| Class: `this.props.`        | Class: `this.state`                     |
### Class State

`/src/compnents/Message.js`
```js
import React, { Component } from "react";

  

class Message extends Component {

    constructor() {
        super()
        this.state = {
            message: 'Welcome from constructor'
        }
    }

    render() {
        return (
        <div>
		// the props in class state
        <h1>{this.state.message}</h1>
        </div>
    );
    }
}

export default Message;
```

#### constructor(props)

 [constructor(props)](https://react.dev/reference/react/Component#constructor): runs before your class component _mounts_ (gets added to the screen). Typically, a constructor is only used for two purposes in React

### onClick

```js
import React, { Component } from "react";

class Message extends Component {

    constructor() {
        super()
        this.state = {
            message: 'Welcome from constructor',
            clickMessage: 'Click Me'
        }
    }
	// Function that change the message    
    changeMessage() {
        this.setState({
            clickMessage: 'You clicked me!'
        })
    }

    render() {
        return (
        <div>
        <h1>{this.state.message}</h1>
        // Arrow function to change the message
        <button onClick={() => this.changeMessage()}>{this.state.clickMessage}</button>
        </div>
    );
    }
}

export default Message;
```
