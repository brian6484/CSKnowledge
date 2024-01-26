## fetch api
It is a modern web api in JS to easily make HTTP requests and fetch response from server.

## frontend to backend
Lets say we need to transfer a dto of fields from the frontend to the backend's controller as a json request. 
The general steps are
1) get the HTMLElement of the fields
example
```js
const id = document.getElementById('id');
```
2) get the values from the HTMLElement and make them into a constant variable
```js
const data = {
    id: id.value,
    /blabla
};
``` 
3) pass that as body into a options constant variable
```js
const options = {
    method,
    headers: {
        'Content-Type': 'application/json',
        // Additional headers if needed
    },
    body: JSON.stringify(data)
};
```

4) this options is an optional parameter that can be passed into fetch() function to customise the HTTP requst.
The object can include
- method : your HTTP methods
- headers: like content-type: application/json
- body : the request payload of data
- mode : mode of request like cors, no-cors, same-origin

5) The fetch function returns a Promise that resolves the Response to that request, whether it is successful or not.

So full example is
```js
const url = 'https://api.example.com/data';
const method = 'POST';

const data = {
    key1: 'value1',
    key2: 'value2'
};

const options = {
    method,
    headers: {
        'Content-Type': 'application/json',
        // Additional headers if needed
    },
    body: JSON.stringify(data)
};

fetch(url, options)
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('Error:', error));

```
