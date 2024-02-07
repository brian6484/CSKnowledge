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


