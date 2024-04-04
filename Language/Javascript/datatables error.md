## Datatables
I have been using datatables for the front-end and I came across this error when trying to implement a search function
that destroys the original table and makes a new one with the searched filters

### cannot reinitialise table
Check the **name of the table** that you are referencing. The name of table on the html has to match with the name of table 
in your JS function that implements this search function. 
