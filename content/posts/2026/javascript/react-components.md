+++
date = '2026-04-22T18:27:56Z'
draft = false
title = 'React: Components'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Components

[Component](https://react.dev/reference/react/Component): is the base class for the React components defined as [JavaScript classes.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) Class components are still supported by React, but we don’t recommend using them in new code.

### The components

1. Root (App)
2. Header
3. Side-Nav
4. Main Content
5. Footer

### In code

Can be `.js` or `.jsx`.

#### Types

- **Stateless functional component**: JavaScript functions that return HTML.
```js
const welcome = (props) => <h1> Hello </h1>;
```
- **Stateful class component**: class extending, render method returning HTML.
```js
class Welcome extends React.Component {
	render() {
		return <h1> Hello, {this.props.name}</h1>;
	}
}
```

### Functional Components

Are JavaScript functions that optionally receive [properties (props)](https://react.dev/reference/react/Component#props)  and return HTML (JSX).

`/src/components/Greet.js`
```js
import React from "react";

// Arrow function
const Greet = () => <h1> Hello Moriel </h1>;

/* // Normal function
function Greet() {
    return <h1> Hello Moriel</h1>;
}
*/

export default Greet
```

`App.js`
```js
// Import the function from the file
import Greet from './compnents/Greet';

function App() {
  return (
    <div className="App">
    // Render is as HTML
      <Greet/>
    </div>
  );
}

export default App;
```

#### export the function

There is a way to export only the function so it will be imported by name.

`/src/components/Greet.js`
```js
import React from "react";

// export Arrow function
export const Greet = () => <h1> Hello Moriel </h1>;
```

```js
// Import the function in {} from the file
import { Greet } from './compnents/Greet';

function App() {
  return (
    <div className="App">
    // Render is as HTML
      <Greet/>
    </div>
  );
}

export default App;
```

### Class Components

Are JavaScript classes (ES6) that optionally receive [properties (props)](https://react.dev/reference/react/Component#props), cam also maintained private state  ,and return HTML (JSX).

`/src/components/Greet.js`
```js
import React, { Component } from "react";

class Welcome extends Component {
    render() {
        return <h1>Class Component</h1>
    }
}

export default Welcome;
```

`App.js`
```js
// Import the function from the file
import Greet from './compnents/Greet';

function App() {
  return (
    <div className="App">
    // Render is as HTML
      <Welcome/>
    </div>
  );
}

export default App;
```

### Functional Component vs Class Components

| **Functional Components**                   | **Class Components**                     |
| ------------------------------------------- | ---------------------------------------- |
| Simple Functions                            | More feature rich                        |
| Use function components as much as possible | Maintain their own private data- `state` |
| Absence of `this` keyword                   | Complex UI logic                         |
| Solution without using `state`              | Provide lifecycle hooks                  |
| Mainly responsible for the UI               | AKA: Stateful / Smart / Container        |
| AKA: Stateless / Dumb / Presentational      |                                          |

