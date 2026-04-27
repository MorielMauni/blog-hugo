+++
date = '2026-04-27T18:53:24Z'
draft = false
title = 'React: Destructuring props and state'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Destructuring props and state

[Destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring): The **destructuring** syntax is a JavaScript syntax that makes it possible to unpack values from arrays, or properties from objects, into distinct variables. It can be used in locations that receive data (such as the left-hand side of an assignment or anywhere that creates new identifier bindings).

### Functions components

`/src/component/Greet.js`
```js
// Extracting `name` and `heroName` from props
const Greet = ({name, heroName}) => {
    return (
    <div>
    // puting only {name} and {heroName}
    <h1> Hello {name} </h1>
    <h2>AKA {heroName}</h2>
    {props.children}
    </div>
);
}

export default Greet
```

Or

```js
const Greet = (props) => {
    const {name, heroName} = props
    return (
    <div>
    <h1> Hello {name} </h1>
    <h2>AKA {heroName}</h2>
    {props.children}
    </div>
);
}

export default Greet
```

### Class components

`src/components/Welcome.js`
```js
class Welcome extends Component {
    render() {
        const {name, heroName} = this.props
        return (
        <div>
        <h1>Welcome, {name}</h1>
        <h2> aka {heroName}</h2>
        </div>
    );
    }
}

export default Welcome;
```
