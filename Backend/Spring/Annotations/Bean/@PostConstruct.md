## @PostConstruct
It marks a method to be executed immediately after the bean's initialisation (right after the constructor and DI are complete, but before the bean is used).
You know initialisation is when Spring container creates an instance out of a class (**bean**) based on the configuration.

Basically you want this method to run **BEFORE** this bean is used in ur application. It could be sth that you must make sure that it runs FIRST.

For example, redisServer() method is being used to initialize and start the embedded Redis server when the Spring Bean containing this method is created.

```java
@PostConstruct
void redisServer() throws IOException {
    log.info("Connect to Embedded-Redis");
    redisServer = RedisServer.builder()
            .port(redisPort)
            .setting("maxmemory 128M")  // Configures the maximum memory for Redis
            .build();
    try {
        redisServer.start();  // Starts the embedded Redis server
    } catch (Exception ignored) {  // Ignores exceptions (likely for graceful error handling)
    }
}
```

