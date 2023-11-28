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

