## Issue
My 

## Another work experience
If there are like 3 business logic methods and you are catching and handling the exception in one of those methods instead of
the main @Tranasactional method, it won't roll back the changes. There are several ways to make sure the main @Transactional
method gets the exception but the most straightforward solution is propagating exception all the way to main method.

BTW **very impt, transactional only rolls back on unchecked exceptions(subclass of RUNTIME EXCEPTIONS)**. If you want it to rollback on other exceptions, you have to add it to **@Transactional(rollbackOn= Exception.class or your other exceptions)**.

### Sol 1 Propagate the exception all the way to the main method
```java
// In the other service
public void someMethod() {
    try {
        // Some code that might throw RuntimeException
    } catch (RuntimeException e) {
        // Log the exception if needed
        logger.error("An error occurred", e);
        // Rethrow the exception
        throw e;
    }
}
```

This throw e will reach the main method.

### Sol 2 Custom exception with GlobalExceptionHandler
ask Claude loll
```java
public class ServiceException extends RuntimeException {
    public ServiceException(String message) {
        super(message);
    }

    public ServiceException(String message, Throwable cause) {
        super(message, cause);
    }
}

@Service
public class YourService {

    @Autowired
    private YourRepository yourRepository;

    @Transactional(rollbackFor = ServiceException.class)
    public YourResponseType performOperation(YourRequestBody body) {
        try {
            // Perform database operations
            YourEntity entity = new YourEntity();
            // Set entity properties based on body
            yourRepository.save(entity);
            
            // Other operations...
            
            return new YourResponseType();
        } catch (Exception e) {
            throw new ServiceException("Error occurred during operation", e);
        }
    }
}

@RestController
public class YourController {

    @Autowired
    private YourService yourService;

    @PostMapping("/your-endpoint")
    public ResponseEntity<YourResponseType> controllerMethod(@RequestBody YourRequestBody body) {
        YourResponseType result = yourService.performOperation(body);
        return ResponseEntity.ok(result);
    }
}

@ControllerAdvice
public class GlobalExceptionHandler {

    private static final Logger logger = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(ServiceException.class)
    public ResponseEntity<String> handleServiceException(ServiceException ex) {
        logger.error("Service exception occurred", ex);
        return ResponseEntity
            .status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body("An error occurred: " + ex.getMessage());
    }

    // You can add more exception handlers for different types of exceptions
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGenericException(Exception ex) {
        logger.error("An unexpected error occurred", ex);
        return ResponseEntity
            .status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body("An unexpected error occurred");
    }
}
```

## Useful Solution with propagating exceptions
```java
@RestController
public class YourController {

    @Autowired
    private YourService yourService;

    @PostMapping("/your-endpoint")
    public ResponseEntity<?> controllerMethod(@RequestBody YourRequestBody body) {
        try {
            YourResponseType result = yourService.performOperation(body);
            return ResponseEntity.ok(result);
        } catch (Exception e) {
            logger.error("Error in controller", e);
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Error occurred");
        }
    }
}

@Service
public class YourService {

    @Autowired
    private YourRepository yourRepository;

    @Transactional(rollbackFor = Exception.class)
    public YourResponseType performOperation(YourRequestBody body) {
        // Perform database operations here
        YourEntity entity = new YourEntity();
        // Set entity properties based on body
        yourRepository.save(entity);
        
        // Other operations...
        
        // If any exception occurs here, it will trigger a rollback
        
        return new YourResponseType();
    }
}

@Repository
public interface YourRepository extends JpaRepository<YourEntity, Long> {
    // Repository methods
}
```
