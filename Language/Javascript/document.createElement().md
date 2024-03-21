## document.createElement()
It is part of Document Object Model (DOM) API that is a programming interface for web documents. This method creates a HTML element.

- document: global 'document' object that basically is the whole HTML document. It provides methods and properties like createElement(), getElementById(), etc
- createElement(): creates HTML element based on specified tag name like option, span, div, etc

So when we do document.createElement(), it creates that element **in-memory**. It doesnt have any attribute or content. So after creating the element, we can either
set the attribute and content OR append it to our DOM. What I mean by appending to DOM is that if we are creating option element, we can append that element to our 
dropdown list (select box).

For example
```js
// Create a new <option> element
const option = document.createElement('option');

// Set attributes and content
option.value = 'optionValue';
option.textContent = 'Option Text';

// Append the option to an existing element in the DOM
const selectElement = document.getElementById('mySelect');
selectElement.appendChild(option);
```

## Why use .createElement()?
It is mainly for dynamic content generation where we want to create HTML elements based on user interaction, incoming
data from API. This allows us to insert them into DOM **without harcoding them into HTML**, reducing loading time and
increasing efficieny.

## Why did I use it?
I was fetching some data from an API, that gave me a list of some cert issue policies from the DB. I created an option HTML element for each of that policy to be included in my search select box (dropbox) so that user can click
on a policy and select it. As seen in the above example we need to set the value and textContent attributes cuz
the HTML element itself dont have any content or attribute.



