## Event Handling
Event handling allows us to respond to user's actions, like if he clicks on something or hovers his mouse on something, etc.

### Example
I prefer that 2nd shortcut example.

```js
// Click event
$('#myButton').click(function() {
    // Handle click event
    // example
    $(this).css('color', 'blue');
});

// Shortcut for click event
$('#myButton').on('click', function() {
    // Handle click event
    $(this).css('color', 'blue');
});
```
