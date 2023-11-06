# Bean Validation

Chapter 3.3.2

## NullPointerException
If data with proper constraints is not passed, you may get a NullPointerException cuz the required field value is not available.

## Advantages 
1) You don't have to manually check each data field value if it meets the desired constraint before passing to Hibernate for storage
2) Bean Validation triggers this validation check *before any db insert or update operations* by recognising constraints assigned on each field attribute
(e.g. @NotNull on String name)
3) It has an automatic SQL schema generation and for each constraint, it generates an SQL equivalent DDL constraint. For example, @NotNull annotation translate to
a SQL NOT NULL constraint for that field attribute.
