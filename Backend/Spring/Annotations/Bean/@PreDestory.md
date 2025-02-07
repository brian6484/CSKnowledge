## @PreDestroy
It marks a method to be destoryed when Spring context is about to be destroyed. (like when app is shutting down or bean is being removed)
```java
@PreDestroy
void stopRedis() {
    if (redisServer != null && redisServer.isActive()) {
        redisServer.stop();  // Stops the embedded Redis server if it is running
    }
}
```
