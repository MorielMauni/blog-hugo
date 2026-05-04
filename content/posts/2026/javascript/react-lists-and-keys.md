+++
date = '2026-05-04T18:20:10Z'
draft = false
title = 'React: Lists and Keys'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

## Lists and Keys

In list rendering we got this error in the console:
```
react-dom-client.development.js:25958 Each child in a list should have a unique "key" prop.

Check the render method of `NameList`. See https://react.dev/link/warning-keys for more information.
```

React is telling us each item should have a prop called `key` and he need to be unique. You can't render `key`, use it with another prop.

```js
// Adding key={person.id}
const personList = persons.map(person => (<Person key={person.id} person={person} />))
```

- A `key` is a special string attribute you need to include when creating lists of elements.
- `key` give the elements a stable identity.
- `key` help React to identify which items have changed, are added, or are removed.
- Help in efficient update of the user interface.

