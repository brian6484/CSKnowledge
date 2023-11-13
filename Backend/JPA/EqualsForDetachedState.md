# Overriding equals() method for comparing entities in detached state
So for entities in detached state, once the EM closes PC, the reference to the instance is still present.
But if it is made persistent again, the JVM now allocates a different in-memory address for this reference so object identity (==) will not work.
But as mentioned in [here][https://github.com/brian6484/CSKnowledge/blob/main/Backend/JPA/JavaIdentityAndEquality.md] in JVM section, the db equality and
object equality still works. So can we use either of these 2?

## DONT use db equality (i.e. same pk value)
What if we implement equals() to compare just the db identifier (pk)? Even if it is detached or not it will have the same pk value so can we do that?
One big problem with this is that id values are not assigned by Hibernate until the instances become persistent (unless it is post-insert strategy 
like TABLE or AUTO but still not a good way). So if a transient instance is added to a Set and we persist it, its hash value would change.
