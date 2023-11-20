# @Id
I didn't know this but once you assign @Id on a field, JPA access fields of that class through field access and considers all fields as persistent 
state by default.

## Natural Key
It is a key with business meaning like tax number (말그대로 내추럴 하니까 비즈니스 의미가 내추럴하게 있음). It should never be used as PK cuz it can complicate things

## Surrogate Key
It is a key with no business meaning.

## Business key
It is a property or a combination of properties that makes each instance unique if they have same database identity (i.e. same pk value).
It is like a natural key except it might change (very rarely though) unlike surrogate key

### Role of business key in defining unique constraint 
Every entity class should have a business class, even if it includes *all** properties of that entity. For example, if we are looking at 
a list of items on the screen, how would we differentiate one item from the other? That differentiating property, or the combination of some
properties, is our business key.

Business key is what we (users) can uniquely identify a particular instance whereas surrogate key is what db uses to uniquely identify. This business key is mostly likely constrained as `UNIQUE` in our db schema. This business key is needed when we need to compare entities in detached state [here][https://github.com/brian6484/CSKnowledge/edit/main/Backend/JPA/EqualsForDetachedState.md]

