+++
date = '2026-04-19T18:05:37Z'
draft = false
title = 'JavaScript: to the website'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Inline JS

`index.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Page Title</title>
    <!-- Link to your CSS file -->
    <link rel="stylesheet" href="style.css">
</head>

<body onload="alert('Hello')"> <!-- Bad pracrice -->
    <h1>Hello, World!</h1>
</body>

</html>
```

### Internal JS

`index.html`:
```html
<body>

<!-- JS script insode of HTML body -->
<script type="test/javascript">
alert("hello");
</script>

</body>
```

### External JS

`index.html`:
```html
<body>

<!-- Points to an external JS file -->
<script src="index.js" charset="itf-8"></script>

</body>
```

- While `styles.css` is in `<head>`, JS scripts is in `<body>`.
- That because JS manipulating the HTML in the body.
- `<script src="index.js" charset="itf-8"></script>` needs to be 1 line above `</body>`.
```HTML
<body>
<h2> bla bla </2>
<h3> bla bla </3>
<!-- Points to an external JS file -->
<script src="index.js" charset="itf-8"></script>
</body>
```


