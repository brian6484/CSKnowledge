## Thread scheduling
In java, it uses priority or Round-robin way.

### Priority
The higher the priority number, the greater the priority.
Values can range from 1 to 10 where Thread.MIN_PRIORITY (1), Thread.MAX_PRIORITY (10), with Thread.NORM_PRIORITY (5) as the default.
```java
thread.setPriority(Thread.MAX_PRIORITY);
thread.setPriority(Thread.NORM_PRIORITY);
thread.setPriority(Thread.MIN_PRIORITY);
```

### Round-robin
Unlike the priority way, it is the JVM which determines the priority, not us users. Round-robin is an algorithm that ensures each thread gets
an **equal share of CPU time** in a cyclic manner.
