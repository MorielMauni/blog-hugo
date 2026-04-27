+++
date = '2026-04-23T19:24:39Z'
draft = false
title = 'React: JSX'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## JSX

[JavaScript XML](https://react.dev/learn/writing-markup-with-jsx): Extension to JavaScript syntax.

- Write XML like code for elements and components.
- JSX tags have a tag name, attributes, and children.
- Not a necessity to write React applications.
- Make React code simpler and elegant.
- Transpiles to pure JS which is understood by the browser.

#### With JSX

[JSX.](https://react.dev/learn/writing-markup-with-jsx): syntax extension for JavaScript that lets you write HTML-like markup inside a JavaScript file. Although there are other ways to write components, most React developers prefer the conciseness of JSX, and most codebases use it.

`/src/componenet/Hello.js`

```js
import React from "react";

const Hello = () => {
    return (
        <div>
            <h1> Hello with JSX </h1>
        </div>
    )
}

export default Hello
```

##### JSX differences

- Class => className
- for => htmlFor
- camelCase property naming conventions:
 	- onclick => onClick
 	- tabindex => tabIndex

#### Without JSX

[React.createElement](https://react.dev/reference/react/createElement): `createElement` lets you create a React element. It serves as an alternative to writing [JSX.](https://react.dev/learn/writing-markup-with-jsx)

```JS
const element = createElement(type, props, ...children)
```

`/src/componenet/Hello.js`

```js
import React from "react";

const Hello = () => {
  return React.createElement(
    "div",
    {id: 'hello', className: 'dummy class'},
    React.createElement("h1", null, "Hello without JSX"),
  );
}

export default Hello
```
