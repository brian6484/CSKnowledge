## REDIS SETNX
Take a look a this code for acquiring a lock via Redis with this setIfAbsent method with redisStringService, which puts this time
as value if there is no such key found.

```java
	@Nullable
	public Boolean setIfAbsent(String key, String value, long time) {
		return redisStringTemplate.opsForValue().setIfAbsent(key, value, time, TimeUnit.MILLISECONDS);
	}
```

```java
    private final static String USER_LOCK_TEMPLATE = "lock_%s";
    private final static String USER_LOCK_VALUE = "%s";
    private final static long USER_LOCK_MAX_DURATION_IN_MILLIS = 1000 * 60 * 10;

    public void lock(long userId) {
        String key = String.format(USER_LOCK_TEMPLATE, userId);
        Instant now = Instant.now();
        String value = String.format(USER_LOCK_VALUE, now.toEpochMilli());

        log.info("user: {} - LOCK TRY", userId);
        try {
            Boolean isSuccessful = redisStringService.setIfAbsent(key, value, USER_LOCK_MAX_DURATION_IN_MILLIS);
            if (isSuccessful == null) {
                redisStringService.delete(key);
                throw new IllegalStateException("Lock may be triggered in transaction.");
            }

            if (!isSuccessful) {
                deleteIfExpired(key);

                log.info("user: {} - ALREADY LOCKED", userId);
                throw new BusinessException(ExceptionStatus.USER_LOCKED);
            }

        } catch (RedisConnectionFailureException e) {
            log.error("user: {} - lock failed due to Redis Connection or Read Failure.", userId, e);
            redisStringService.delete(key);
            throw e;
        }

        log.info("user: {} - LOCK SUCCESS", userId);
    }
```

But then, how is this linked to acquriing a lock? Actually this action of **atomically setting a value only if a key doesnt exist**
is used to implement distribute locking. This is known as **SETNX** (set if not exists). Rmb atomic means that operation is invisible and
indivisible (cannot be split to smaller parts) and instantaneous (occur instantly).

So for example,
Lock Key: Each user has a unique lock key, e.g., "lock_123" for user with ID 123.

Lock Value: The value can be a unique identifier or a timestamp, (latter in our example) e.g., "1631925048000" representing the current timestamp.

Acquiring the Lock: When a user attempts to acquire a lock, the application uses SETNX to set the value of the lock key **only if** it does 
not exist. If successful, the user effectively acquires the lock. This way, **only 1 user** can set this lock key (cuz it is unique), 
preventing other users from acquiring that same lock. It's a basic way to coordinate access to a shared resource (in this case, preventing 
duplicate requests) in a distributed environment.



