## layout.html not being imported to my html files
I was trying to put layout.html in my update.html but it is not being imported properly. I did

```html
<html layout:decorate="~{fragments/layout}">
```

which worked in my previous project. I first checked if there is like a wrong reference to a misplaced file in that layout.html but the file imports were fine.
I chat-gpted and apparently you need to import Thymeleaf Layout Dialect and set some settings in yml. This TLD is an **Extension** of Thyemleaf that provides additional
functionalities like layout's decorate, which I was trying to use, which allows us to specify a template to be used as a layout. Other html files can then insert 
content into (decorate) this existing layout to build upon it.

```yml
implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'

spring:
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
    cache: false
```

