# @Id
I didn't know this but once you assign @Id on a field, JPA access fields of that class through field access and considers all fields as persistent 
state by default.

## Natural Key
It is a key with business meaning like tax number. It should never be used as PK cuz it can complicate things

## Surrogate Key
It is a key with no business meaning.
