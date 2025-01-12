## Using Enum for exception
Refer to my [Enum](https://github.com/brian6484/CSKnowledge/blob/main/Language/Java/General/Enum.md) on how it works.
We wanna create a constructor to contain additional info for our exceptions - like HttpStatus, message, etc.

Best practice:
The constructor for ExceptionStatus takes four parameters:

HttpStatus httpStatus: This parameter sets the httpStatus field, representing the HTTP status associated with the exception. For example, BAD_REQUEST for 400 errors, UNAUTHORIZED for 401 errors.

String codeType: This parameter sets the codeType field, which categorizes the type of error, such as AUTH (authentication-related) or USER (user-related). These are defined in the inner static Code class.

String code: This parameter sets the code field, representing a unique code identifying the specific error within its category (e.g., "001" for a specific authentication error).

String message: This parameter sets the message field, providing a detailed description of the exception in either English or Korean.

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
