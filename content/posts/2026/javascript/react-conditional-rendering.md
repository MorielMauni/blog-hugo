+++
date = '2026-05-02T19:02:18Z'
draft = false
title = 'React: Conditional Rendering'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Conditional Rendering

[Conditional Rendering](https://react.dev/learn/conditional-rendering): Your components will often need to display different things depending on different conditions. In React, you can conditionally render JSX using JavaScript syntax like:
1. `if`/`else`.
2. Element variables: `&&`, `? :`
3. Ternary conditional operator.
4. Short circuit operator.

#### `if` / `else`

```js
import React, { Component } from 'react'

// rce
class UserGreeting extends Component {
    
    constructor(props) {
      super(props)
    
      this.state = {
         isLoggedIn: true
      }
    }
  
    render() {
        
        if (this,this.state.isLoggedIn) {
            return (
                <div>Welcome Moriel</div>
            )
        } else {
            return (
                <div>Welcome Stranger</div>
            )
        }
  }
}

export default UserGreeting
```

#### Element variables

JavaScript to store elements.

```js
import React, { Component } from 'react'

// rce
class UserGreeting extends Component {
    
    constructor(props) {
      super(props)
    
      this.state = {
         isLoggedIn: true
      }
    }
  
    render() {
    
    // Variable
    let message
    // If-else statement
    if (this.state.isLoggedIn) {
        message = <div>Wellcome Moriel</div>
    } else {
        message = <div>Wellcome Stranger</div>
    }
    // Return to render
    return message = <div>{message}</div>
  }
}

export default UserGreeting
```

#### Ternary conditional operator

Can be used inside JSX. Most of the time.

```js
import React, { Component } from "react";

// rce
class UserGreeting extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isLoggedIn: true,
    };
  }

  render() {
    return (
      // Is True or False
      this.state.isLoggedIn ? (
        // If True
        <div>Welcome Moriel</div>
      ) : (
        // If False
        <div>Welcome Stranger</div>
      )
    );
  }
}

export default UserGreeting;
```

#### Short circuit operator

Specific case of Ternary conditional operator, if you want to **render nothing** or something

```js
import React, { Component } from "react";

// rce
class UserGreeting extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isLoggedIn: true,
    };
  }

  render() {
  // if it's True, return this, if False, return nothing
    return this.state.isLoggedIn && <div>Welcome Moriel</div>
  }
}

export default UserGreeting;

```
