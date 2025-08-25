## volatile
ensures changes made by one thread are visible to other threads immediately. **BUT IT DOESNT ENSURE ATOMICITY** like
count++

### how
without volatile, threads cache variables locally (in cpu register/thread memory) but with volatile, every read
**comes from main memory** and writes are done onto main memory 

## synchronised
ensures visibility AND mutual exclusion (atomicity) so count++ works

### how 
it uses locks and unlocking
