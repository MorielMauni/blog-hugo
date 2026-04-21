+++
date = '2026-04-21T20:18:46Z'
draft = false
title = 'React: First App'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### What is React

React: Java script library, focus on UI, rich ecosystem.
- Component based architecture:
	- Header, Side-Navigator, Main content, and a footer.
- Re-useable code (Angular and Vue).
- Declarative: tell him what you want, and he builds.
- DOM updates are handle by React.

### Pre-requisites

- HTML.
- CSS.
- JavaScript (ES6).
	- `this` keyword, `filter`, `map` and `reduce`
	- `let` & `const`, arrow functions, template literals, default parameters, object literals, rest and spread operators and destructuring assignment.

### First App

#### Need

1. Install Node.js
2. Code Editor.
#### Create

```bash
npx create-react-app <project-name>
cd <project-name>
npm start
```

Open `/src/App.js`:
edit:
```js
<p> Hello World </p>
```

### Folders structure


- `package.json`: the dependencies and scripts for the project.
- `node_modules`: created with npx.
- `/public/index.html`: the HTML file for the project (single page).
	- In the body`<div id="root"></div>`.
- `/src/`: main working directory:
	- `index.js`: root component: 
		- `const root = ReactDOM.createRoot(document.getElementById('root'));`
	- `App.js`: The view in the browser.
	- `App.css`: styling.
	- `App.test.js`: testing.
