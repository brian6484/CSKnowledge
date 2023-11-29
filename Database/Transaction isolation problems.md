# transaction isolation prob
## lost update
![Screenshot 2023-11-21 161509](https://github.com/brian6484/CSKnowledge/assets/56388433/81bfea45-ea0b-4c17-a51e-37ef161d182d)

2 concurrent transactions update the same info in db. In the example, the first transaction writes value but is overwritten by the second one so there is lost update.
The last commit wins. 

## dirty read
![Screenshot 2023-11-21 161521](https://github.com/brian6484/CSKnowledge/assets/56388433/00d5a6c4-f7f4-4e3d-8078-ce592a6c395a)

If transaction 2 reads the changed values that is made by transaction 1, **that is not committed yet!!** and is still in the middle of that unit of work!
That data is invalid

## unrepeatable read
![Screenshot 2023-11-21 161532](https://github.com/brian6484/CSKnowledge/assets/56388433/79246140-da95-4ca1-b22f-df09350b021b)

if a transaction reads data item twice and reads different states/values each time. For example, another transaction might have changed the data that this particular
transaction is reading and **comitted** that data. So effectively the data is changed and persisted.

## phantom read
![Screenshot 2023-11-21 161543](https://github.com/brian6484/CSKnowledge/assets/56388433/83e90557-8eb0-49f0-8746-d1e9fc3c3b51)

if transaction queries for the same data twice, the second results contains more or less data than the first read because something is either added or deleted
