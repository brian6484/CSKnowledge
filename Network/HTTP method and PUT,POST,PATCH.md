## HTTP methods
The commonly seen methods are get, post, delete and put. Oh i dint know diff between get and post is get requests are cached by browser so that when same request
is made, it can return the cached response. But post requests are not cached.

## The tricky ones
Difference between POST and PUT is that POST is when you are entering new data that doesnt exist yet in db. PUT is used to *edit* the data saved in db.
But what about PATCH? PUT requires **all** data to be passed, even the data that you don't want to change. But with PATCH, you can just edit the data you wanna change 
without being forced to pass all the data. Literally think of LOL game path where just 부분 partial patch of certain data like game updates are called patch and not PUT cuz it is modifying just specific parts of the software, not PUT all data from scratch.
