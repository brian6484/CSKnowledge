## beforehand
We need to understand how html elements are created [first](https://github.com/brian6484/CSKnowledge/tree/main/Language/Javascript).


## dropdown or select box (<select> element) with fetch API
First in your html file, the `<select>` element is used to create a dropdown list, with unique id of searchSelect.

```js
<body>
    <h1>Data Search and Selection</h1>

    <!-- Search input with scrollbar -->
    <div id="searchContainer">
        <select id="searchSelect">
            <!-- Options will be appended here dynamically -->
        </select>
        <div id="searchResults"></div>
    </div>
</body>
```

Then in your js file, and as explained as the beforehand link, we need to **create an option element** for our dropdown, 
not just other elements cuz it wont work for other elements. Then, we are setting the attribute and content with the 
data from our fetch API that returns a list of strings and finallly appending to searchSelect, our dropdown box.
```js
document.addEventListener('DOMContentLoaded', function () {
    const searchSelect = document.getElementById('searchSelect');

    // Fetch data from the server and populate the select box
    fetch('/api/certissuepolicy/list')
        .then(response => response.json())
        .then(data => {
            if (Array.isArray(data)) {
                // Append options to the select element
                data.forEach(item => {
                    const option = document.createElement('option');
                    option.value = item;
                    option.textContent = item;
                    searchSelect.appendChild(option);
                });
            } else {
                console.error('Data is not an array:', data);
            }
        })
        .catch(error => console.error('Error:', error));

    // Add any additional event listeners or functionality as needed
});
```
