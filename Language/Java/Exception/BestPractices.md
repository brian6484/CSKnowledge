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
    // AUTH
    //// 400 BAD REQUEST
    OIDC_ISSUER_WRONG(BAD_REQUEST, Code.AUTH, "001", "잘못된 로그인 경로입니다."), // OIDC issuer가 구글, 페북, 애플이 아닐 때
    NOT_VERIFY_OAUTH(BAD_REQUEST, Code.AUTH, "002", "인증 번호가 만료되었습니다."),
    NOT_AGREE_SERVICE_TERM(BAD_REQUEST, Code.AUTH, "003", "not agree service term"),
    NICKNAME_VERIFY_EXPIRATION(BAD_REQUEST, Code.AUTH, "004", "닉네임 정보가 만료되었습니다."),
    DISABLE_REGISTER(BAD_REQUEST, Code.AUTH, "005", "회원가입 정보가 만료되었습니다."),
    ALREADY_REGISTER(BAD_REQUEST, Code.AUTH, "006", "이미 가입한 계정입니다."),
    NOT_VERIFY_PHONE(BAD_REQUEST, Code.AUTH, "007", "not verify phone"),
    SEND_SECRET_VALID_TIME_EXCEEDED(BAD_REQUEST, Code.AUTH, "009", "30초 이내에는 인증번호를 다시 전송할 수 없어요."),
    SEND_SECRET_COUNT_EXCEEDED(BAD_REQUEST, Code.AUTH, "010", "인증 문자는 하루에 최대 6회 받을 수 있어요. 내일 다시 시도해주세요."),
    PHONE_VERIFY_COUNT_EXCEEDED(BAD_REQUEST, Code.AUTH, "011", "인증번호 입력 시도 횟수가 초과되었습니다."),
    PHONE_VERIFY_SECRET_INVALID(BAD_REQUEST, Code.AUTH, "012", "인증번호가 올바르지 않아요!"),
    REGISTER_RESTRICTED_AFTER_DELETE(BAD_REQUEST, Code.AUTH, "013", "탈퇴 후 7일 간은 재가입이 불가능합니다."),
    RECOMMEND_CODE_CONTAIN_SPACING(BAD_REQUEST, Code.AUTH, "014", "띄어쓰기를 사용할 수 없습니다."),
    RECOMMEND_CODE_INVALID(BAD_REQUEST, Code.AUTH, "015", "올바른 추천인 코드 양식이 아닙니다."),

    // 401 UNAUTHORIZED
    CUSTOM_AUTHENTICATION_ENTRYPOINT(UNAUTHORIZED, Code.AUTH, "100",
            "invalid jwt"), // 전달한 Jwt 이 정상적이지 않은 경우 발생 시키는 예외, Authentication 객체가 필요한 권한을 보유하지 않은 경우 발생합
    TOKEN_INVALID_EXCEPTION(UNAUTHORIZED, Code.AUTH, "101",
            "invalid token"), // access or refresh token이 유효하지 않을 때
    TOKEN_EXPIRED_EXCEPTION(UNAUTHORIZED, Code.AUTH, "102", "expired token"), // 만료된 토큰
    KAKAO_ACCESS_TOKEN_INVALID(UNAUTHORIZED, Code.AUTH, "103",
            "wrong kakao access token"), // access token으로 받아온 kakao id가 idToken id와 다를 경우


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
