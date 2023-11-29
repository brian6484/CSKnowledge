# Synchronous Asynchronous Blocking Nonblocking
## Synchronous
<img width="807" alt="동기1" src="https://github.com/brian6484/CSKnowledge/assets/56388433/27c3c8db-531c-4b22-b192-ba990190d6f6">

When a request for a task is sent, you must wait until you receive a response back to your request before continuing.
It is a sequential order, something we are used to seeing. The *calling function* is concerned about the completion of task.

In the example, if work that thread 1 was trying to process is sent to thread 2, thread 1 must wait until that task is completed
by thread 2 (**waiting state**) before continuing.

<img width="712" alt="동기4" src="https://github.com/brian6484/CSKnowledge/assets/56388433/615be12f-1b0f-46a6-b971-254cb382af62">

## Aynchronous
<img width="809" alt="비동기1" src="https://github.com/brian6484/CSKnowledge/assets/56388433/2c54fbdf-f5fe-4584-89ae-aac23ef08a80">

When a request for a task is sent, we don't have to wait for a response back and we can just continue with our own tasks.
Neither the *calling function* or *callback function* cares about the completion of task.

<img width="714" alt="비동기4" src="https://github.com/brian6484/CSKnowledge/assets/56388433/88b22717-efc4-470e-ba9b-df22a3b5a1ff">

## Blocking
<img width="797" alt="블로킹1" src="https://github.com/brian6484/CSKnowledge/assets/56388433/ed8f6bc3-16e8-45a3-900e-be54d0c6abd8">

When function A is executed, if control is passed to function B, function A MUST wait for B to finish cuz it has *no control*. Once control is returned,
function A continues from where it has left off.

<img width="711" alt="블로킹2" src="https://github.com/brian6484/CSKnowledge/assets/56388433/7b45b7a0-090b-4f7d-80f2-7ef0c5d5ba22">

## Non blocking
<img width="815" alt="논블로킹1" src="https://github.com/brian6484/CSKnowledge/assets/56388433/8ab92e72-416f-43e1-ac0f-376bc52e9a29">

If function B is invoked when function A is being executed, function A **does not** pass control to B and continues its work 
**regardless of whether function B completes**.

<img width="706" alt="논블로킹2" src="https://github.com/brian6484/CSKnowledge/assets/56388433/1e6bcbb1-129f-4df7-b946-81701c8977d9">

## Combinations
![Screenshot 2023-11-29 105309](https://github.com/brian6484/CSKnowledge/assets/56388433/64700b52-5294-4928-8391-4413307764cd)

### syn + block
<img width="712" alt="동기_블로킹1" src="https://github.com/brian6484/CSKnowledge/assets/56388433/d4f13151-8a0f-4f9e-960d-a851472fe7f8">

So syn means calling function keeps checking called function if it has completed the work while blocking means control is passed to that called function (task1).
So thead 1 must wait for task1 to be completed.

<img width="675" alt="동기_블로킹2" src="https://github.com/brian6484/CSKnowledge/assets/56388433/7167a1b2-d5c4-4e16-a936-3a0f9589e1d7">

### syn + non-blocking
<img width="746" alt="동기_논블로킹1" src="https://github.com/brian6484/CSKnowledge/assets/56388433/74c3cd91-5143-4bf5-87a0-0053d9174b5c">

Syn means called function is constantly checked if it has completed work but non blocking means control is not passed to called function so 
it coninues its work but keeps checking if work is completed.

For example, when updating lol client, the remaining time and percentage is constantly fetched and up to date - which means it is constantly being checked (syn)

<img width="696" alt="동기_논블로킹2" src="https://github.com/brian6484/CSKnowledge/assets/56388433/92b30145-1427-41a1-878e-bdd68f84f982">

## asyn + block
<img width="748" alt="비동기_블로킹1" src="https://github.com/brian6484/CSKnowledge/assets/56388433/f70ab6b2-64d8-4094-b0f9-56d8836de8d7">

So suprisingly async means it doesnt care if task is completed but in this combination, the callback function checks if task is completed while
blocking means task 1 has control. This is cuz while thread 1 doesnt care if thread 2 successfully completes task 1 or not, since control is passed to thread 2,
thread 1 has no choice but to be in waiting state until work is completed.

<img width="643" alt="비동기_블로킹2" src="https://github.com/brian6484/CSKnowledge/assets/56388433/2a94a42e-98c2-46f4-be61-bdbf97406822">

## asyn + non-blocking
<img width="728" alt="비동기_논블로킹1" src="https://github.com/brian6484/CSKnowledge/assets/56388433/2bf20e11-20dc-40ef-a258-0b5735609bf4">

So thread 1 can continue its work regardless of if task 2 by thread 2 is completed or not. It is best in terms of performance and memory eff.

<img width="724" alt="비동기_논블로킹2" src="https://github.com/brian6484/CSKnowledge/assets/56388433/e748cd11-c172-47e9-91b9-ebf7dcb89242">
