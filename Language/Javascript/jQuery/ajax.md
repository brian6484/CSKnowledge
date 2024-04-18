## AJAX
It allows us to fetch data from server **without having to reload page**.

### Example
For e.g. we wanna get some data from an API endpoint with AJAX get request and display it on our webpage, specifically
iterating through each data in our JSON data response with `.forEach()` method and appending it to our list with `.append()`.


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AJAX Example</title>
    <!-- Include jQuery library -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
    <!-- Include custom script -->
    <script src="script.js"></script>
</head>
<body>
    <div id="data-container">
        <h2>Data:</h2>
        <ul id="data-list"></ul>
    </div>
    <button id="fetch-data-btn">Fetch Data</button>
</body>
</html>
```

```js
$(document).ready(function() {
    $('#fetch-data-btn').click(function() {
        // Make an AJAX request to fetch data from the server
        $.ajax({
            url: 'https://api.example.com/data',
            method: 'GET',
            dataType: 'json', // Specify the expected data type
            success: function(data) {
                // Handle successful response
                displayData(data);
            },
            error: function(xhr, status, error) {
                // Handle error
                console.error('Error:', error);
            }
        });
    });

    function displayData(data) {
        // Clear existing data
        $('#data-list').empty();

        // Iterate over the fetched data and append to the list
        data.forEach(function(item) {
            $('#data-list').append(`<li>${item.name}</li>`);
        });
    }
});
```

### Initial dilemma
Wait when we said we are gonna append each iteration of our data to our **list**, well we can see that a html id of
*data-list* has been created. But this is just the name isnt it? Like how do we know it is a *list*? 

Well fug me. We declared it with ul tag. ul literally represents an **unordered list**. So it is a list lol

### Explanation of code
fetch-data-btn button is first selected via $('selector') and once it is clicked, AJAX request is sent. Once we get data
from that endpoint, displayData takes data as argument and first clears any existing data in `data-list` element. Then,
we iterate using `.forEach()` and creates `<li> element to be appended to the unordered list.
