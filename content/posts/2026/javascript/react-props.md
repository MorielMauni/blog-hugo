+++
date = '2026-04-24T11:12:26Z'
draft = false
title = 'React: Props'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Props


[Properties](https://react.dev/learn/passing-props-to-a-component): the mechanism used to pass data and configuration from a parent component to a child component. They function similarly to arguments in a JavaScript function or attributes in an HTML. **Immutable**.

`App.js`
```js
import Greet from './compnents/Greet';

function App() {
	return (
		<div className="App">
		// Add a propertie and a value
			<Greet name="Eliya"/>
			<Greet name="Mazal"/>
			<Greet name="Isikit"/>
		</div>
	);
}

export default App;
```

`/src/components/Geet.js`
```js
import React from "react";

const Greet = (props) => {
	//  Return the <h1> with the `props.property`
    return <h1> Hello {props.name} </h1>;
};

export default Greet;
```

#### Only 1 prop at a time

You can return only 1 property each time:

`/src/components/Geet.js`
```js
import React from "react";

const Greet = (props) => {
    return (
	// We return 1 div that have inside an h1, h2, and children.
    <div>
    <h1> Hello {props.name}</h1>
    <h2>AKA {props.heroName}</h2>
    {props.children} // Render nothing if empty
    </div>
);
}

export default Greet
```

`App.js`
```js
import Greet from './compnents/Greet';

function App() {
	return (
		<div className="App">
		// Return 2 props with a children
			<Greet name="Eliya" heroName="Batman">
		     <p>This is a child prop</p>
		     </Greet>
		     <Greet name="Mazal" heroName="Superman">
		     <button> Action </button>
		     </Greet>
		     <Greet name="Isikit" heroName="Spiderwoman"/>
		</div>
	);
}

export default App;
```

### Props with Class component 

In a Class component

`/src/component/Welcome.js`
```js
import React, { Component } from "react";

class Welcome extends Component {
    render() {
        return (
        <div>
        <h1>Welcome, {this.props.name}</h1>
        <h2> aka {this.props.heroName}</h2>
        </div>
    );
    }
}

export default Welcome;
```
