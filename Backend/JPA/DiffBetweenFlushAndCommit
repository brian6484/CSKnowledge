# Difference between em.flush() and transaction.commit()
## Diff
I know that both synchronises changes in PC to the DB but I wasnt sure about the diff, especially does flush send SQL statements to DB?
YES! If it doesn't, there is no way to update the changes in the DB lol so obviously it sends SQL statements.
But the difference is that flush doesnt end the transaction so you can call flush multiple times to update the DB explicitly. (but 
you shouldnt do this unless you really need the latest changes saved in DB for next code to get that DB-updated (not just PC-updated) entity).
Commit, however ends the transaction, so it automatically calls flush to synchronise the changes one last time before ending the transaction.
