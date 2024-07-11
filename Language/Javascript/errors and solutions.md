## Error 1
If you are not seeing immediate changes in your browser after you edited your JS files, you should clear
your browser cache via Google's three dots -> "More tools" -> "Clear browsing data."

But why does this solve the issue? Browsers cache the results of your requests or copies of resources like your
JS, CSS and images. So browser might just return them instead of fresh new data.

## required request parameter 'hiddenvalue1' for method type Long is not present
Not JavaScript but html erorr.
When posting to a controller with some fields, the html MUST INCLUDE NAME ATTRIBUTE. for the input field, I just included id attribute but I got that error cuz the name was not set properly so the field value was not send to my controller.

correct
```
<input id="hiddenValue1" name="hiddenValue1">
```
