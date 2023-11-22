## HTTP methods
The commonly seen methods are get, post, delete and put.

## The tricky ones
Difference between POST and PUT is that POST is when you are entering new data that doesnt exist yet in db. PUT is used to *edit* the data saved in db.
But what about PATCH? PUT requires **all** data to be passed, even the data that you don't want to change. But with PATCH, you can just edit the data you wanna change 
without being forced to pass all the data 
