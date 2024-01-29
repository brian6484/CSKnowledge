## Spring AOP
It works on proxy pattern, which briefly means it doesnt reference the actual objects but the **proxy objects**. But why?
Why not use the actual objects?

## Referencing actual objects
Lets say AOP is designed to ref actual objects. To call the *Advice" functionality in our business logic code, we have
to invoke that **within Target class**, which is basically doing the same thing if there were no AOP. 
