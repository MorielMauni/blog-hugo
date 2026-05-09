+++
date = '2026-05-09T19:42:01Z'
draft = false
title = 'React Router'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### React Router

Install [React Router](https://reactrouter.com/):
```bash
npm install react-router-dom
```

- Routing is on client side.

`main.jsx`
```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'
import './index.css'

// Routes
import { RouterProvider, createBrowserRouter } from 'react-router-dom'

const router = createBrowserRouter([
  // Takes a list of routes
  { path: '/', element: <App /> }, // root
  { path: '/create-post', element: <NewPost /> },
]);

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <RouterProvider router={router}/> {/*  */}
  </React.StrictMode>
)
```

[createBrowserRouter](https://reactrouter.com/6.30.3/routers/create-browser-router#createbrowserrouter): 

### Layout routes

Some routes can have shared elements.
Nested routes.

Create `src/routes/` to organize the components used as routes.

`RootLayout.jsx`
```jsx
import React from 'react'
import MainHeader from '../components/MainHeader';
import { Outlet } from 'react-router-dom';

function RootLayout() {
  return (
    <>
        <MainHeader />
        <Outlet />  {/* Returns the <App/> contanet back  */}
    </>
  )
}

export default RootLayout;
```

`main.jsx`
```jsx
// Imports from React and ReactDOM
import React, { Children } from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'
import './index.css'

// Routes
import { RouterProvider, createBrowserRouter } from 'react-router-dom'
import NewPost from './components/NewPost'
import RootLayout from './routes/RootLayout'

const router = createBrowserRouter([
  // Takes a list of routes
  { 
    path: '/', 
    element: <RootLayout />, // The root path have it
    children: [
        { path: '/', element: <App /> },
        { path: '/create-post', element: <NewPost /> },
    ]
  },
]);
//

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <RouterProvider router={router}/>
  </React.StrictMode>
)

```

