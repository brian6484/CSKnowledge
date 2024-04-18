## Intro
jQuery is a JS library that simplfies the complexities of JS by providing an easy-to-use API to write JS code.

### Including jQuery
```js
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
```

### Document Ready Event
To ensure that jQuery logic runs **after all HTML has been loaded**, we use 
```js
$(document).ready(function() {
    // jQuery code here
});

// Shorthand syntax
$(function() {
    // jQuery code here
});
```

This is important cuz jQuery logic will most probably change elements in DOM and so we want to load all elements first 
before changing them.

### Selecting elements
It uses CSS style selectors, where you can select class, tag name, id, attribute, etc. We use $('selector'), which
very importantly, selects not just one but **can select multiple** elements from DOM.

```js
// Select by tag name
$('p')

// Select by class
$('.example')

// Select by ID
$('#myElement')

// Select by attribute
$('[name="username"]')
```

#### select via attribute
That above select by attribute looks for element that has `name` attribute with `username` value.

for example like this in html
```html
<input type="text" name="username" />
```

Other select by attributes can include:
```js
//Selects all input elements with a type attribute equal to "text".
$('input[type="text"]'):

//Selects all anchor elements (<a>) with an href attribute equal to "#".
$('a[href="#"]'):

//Selects all image elements (<img>) that have an alt attribute.
$('img[alt]'): 
```

### Manipulate elements
You can manipulate content of a selected element (that you have just selected via $('selector')). You can change
the text, add or remove class, change css style, etc
```js
// Change text content
$('#myElement').text('New text');

// Change HTML content
$('#myElement').html('<strong>New HTML content</strong>');

// Add or remove classes
$('#myElement').addClass('highlight');
$('#myElement').removeClass('highlight');

// Set CSS properties
$('#myElement').css('color', 'red');
```





