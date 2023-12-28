## CompletableFuture
Coming back from the box and channel model post, let's explore completeablefuture.

The problem with Future is that it is an **interface**, so it is limited in composition (chaining multiple async tasks tgt),
error handling, lack of cancellation, etc.

