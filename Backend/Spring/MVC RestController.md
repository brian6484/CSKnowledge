## Diff betweeen Controller and RestController
Controller is just returning a view or ModelAndView objects. This is for MVC pattern.

RestController is for building RESTful apis 

## RestController
If in a form you want to pass some parameter values to your API, you need @RequestParam annotation at controller level.
But how do we pass those values into our parameter?

Normally, you would pass those values in a **form** with a submit button like this

```html
<form th:action="@{/register}" method="post" enctype="multipart/form-data" id="myForm">
    <input type="file" id="multipartFile" name="multipartFile"><br>
    <input type="hidden" id="hiddenValue" name="longValue"><br>  

    <button type="submit" onclick="setHiddenValue()">Submit</button>  <!-- Set hidden value before submitting -->
</form>
```

## CAREFUL OF THIS MISTAKE
My API kept saying it couldnt get the hiddenValue value. But my html was like
```html
<input type="hidden" id="hiddenValue"><br>
```

Notice the error? You need to explicitly name the value as "hiddenValue". The *name* attribute in the form's input field
**has to match** with the expected parameter of the controller.
```html
<input type="hidden" id="hiddenValue" name="longValue"><br> 
```
