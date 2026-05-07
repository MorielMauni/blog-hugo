+++
date = '2026-05-07T17:52:52Z'
draft = false
title = 'React: Back-end to the React SPA'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Intro
This was a part from a learning project of recipe website.

For now, the posts are locally and deleted when the page is refreshed.
We need an API that will handle HTTP requests and responses.


### POST

`PostsList.jsx`
```jsx
function PostList({ isPosting, onStopPosting }) {
    
    function addPostHandler(postData) {
        // Send/Fetch HTTP request
        fetch('http://localhost:8080/posts', {
            method: 'POST', // default is 'GET'
            body: JSON.stringify(postData), // Send data as JSON only
            headers: {
                'Content-Type': 'application/json'
            }
        });
```

 [fetch()](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API): The Fetch API provides an interface for fetching resources (including across the network). The Fetch API uses [`Request`](https://developer.mozilla.org/en-US/docs/Web/API/Request) and [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response) objects (and other things involved with network requests), as well as related concepts such as CORS and the HTTP Origin header semantics.

### GET with useEffect

[useEffect()](https://react.dev/reference/react/useEffect):  is a React Hook that lets you [synchronize a component with an external system.](https://react.dev/learn/synchronizing-with-effects)

```jsx
import { useState, useEffect } from 'react';

// takes a function and an array.
useEffect(() => {}, []);
```

`PostsList.jsx`
```jsx
import { useState, useEffect } from "react";

function PostList({ isPosting, onStopPosting }) {

    useEffect(() => {
        async function fetchPost() { // asynchronous function
	    //  Reaching to back-end and getting the posts.
            const response = await fetch('http://localhost:8080/posts')
        //  Data is raw, re-format the data    
            const resData = await response.json();
        // set the state value    
            setPosts(resData.posts);
        }

	//  trigger the function
        fetchPost();
    }, []); // Empty array = React loads only when the component mounts.
```
