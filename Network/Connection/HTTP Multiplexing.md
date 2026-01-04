## Multiplexing
Multiplexing, which is **sending multiple requests and reponses over the same connection simultaneously
WITHOUT WAITING FOR PREVIOUS ONE TO FINISH**.

### Without multiplexing
Each request has to wait until previous request completesm which causes delays (called **head-of-line blocking** in HTTP/1.1)

### With multiplexing
Server can send responses *as soon as they are ready** even when earlier requests havent finished

## Benefits of multiplexing 
1) reduces latency
2) makes loading faster cuz multiple resources can be fetched **in parallel over ONE connection**, not multiple connections
3) Avoids overhead of opening multiple TCP connections

## Analogy
HTTP/1.1 = one-lane road → cars (requests) must wait in line.

Multiplexing = multi-lane highway → cars move independently and overtake each other.
