## Using Enum for exception metadata
Refer to my [Enum](https://github.com/brian6484/CSKnowledge/blob/main/Language/Java/General/Enum.md) on how it works.
We wanna create a constructor to contain additional info for our exceptions - like HttpStatus, message, etc.

Best practice:
The constructor for ExceptionStatus takes four parameters:

1) HttpStatus httpStatus: This parameter sets the httpStatus field, representing the HTTP status associated with the exception. For example, BAD_REQUEST for 400 errors, UNAUTHORIZED for 401 errors.

2) String codeType: This parameter sets the codeType field, which categorizes the type of error, such as AUTH (authentication-related) or USER (user-related). These are defined in the inner static Code class.

3) String code: This parameter sets the code field, representing a unique code identifying the specific error within its category (e.g., "001" for a specific authentication error).

4) String message: This parameter sets the message field, providing a detailed description of the exception in either English or Korean.

```java
@RequiredArgsConstructor
@Getter
public enum ExceptionStatus {
    // USER
    // 404 NOT FOUND
    USER_NOT_FOUND(NOT_FOUND, Code.USER, "400", "User not found"),

    // AUTH
    // 500 INTERNAL SERVER ERROR
    ENCRYPT_ERROR(INTERNAL_SERVER_ERROR, Code.AUTH, "500", "Encrypt error"),;

    private final HttpStatus httpStatus;

    private final String codeType;

    private final String code;

    private final String message;

    public String getCode(){
        return this.codeType + code;
    }

    private static class Code {
        private final static String USER = "USR";
        private final static String AUTH = "ATH";
    }
}

```

## That is not it
So this exceptionstatus is not the actual exception that we are gonna throw. There are mainly 2 reasons.
1) In java, only classes that extend **Throwable** like Exception/RuntimeException can be thrown. This enum does not so it cannot be thrown.
2) Separation of concerns - ExceptionStatus enum contains just the metadata and we need the **actual exception**.

## The actual Exception
So we need the actual exception which extends this throwable. Imma call it BusinessException.
There is an optional field of String arguments.
```java
@Getter
public class BusinessException extends RuntimeException{
    private final ExceptionStatus exceptionStatus;
    private final String[] args;


    public BusinessException(final ExceptionStatus exceptionStatus) {
        this.exceptionStatus = exceptionStatus;
        this.args = null;
    }

    public BusinessException(final ExceptionStatus exceptionStatus, final String ... args) {
        this.exceptionStatus = exceptionStatus;
        this.args = args;
    }
}
```
## How to use our custom BusinessException
Remember the [anti pattern of Optional](https://github.com/brian6484/CSKnowledge/blob/main/Language/Java/Optional%20and%20anti-patterns.md). This is the right way.
```java
public User findById(Long userId) {
    return userRepository.findById(userId)
        .orElseThrow(() -> new BusinessException(ExceptionStatus.USER_NOT_FOUND));
}

```
