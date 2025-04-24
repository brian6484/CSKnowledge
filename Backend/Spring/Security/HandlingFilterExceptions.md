
## Filter's exceptions are not caught by ExceptionHandler
As you see in [here](![Image](https://github.com/user-attachments/assets/374a200a-d03b-496f-a628-a05ff8640c4d)), Filters operate at the Servlet level, which is before the request reaches your Spring MVC controllers. They intercept incoming HTTP requests and outgoing HTTP responses.
And since filters are before the servlet level, exceptions caused here are not caught by CA.

So to explicitly handle exceptions in filter level, you need to set some logic within it.
For example I did like this. Notice That if any business exceptions re caused, I catch it and make the error response message **the same format** as my global
exception handler cuz from the client side, they want a consistent way to handle responses.

```java
    @Override
    protected void doFilterInternal(
            HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        try {
            String token = HttpHeaderUtils.getTokenFromAuthHeader(request);

            if (!StringUtils.hasText(token)) {
                setAuthenticationToContext(createGuestAuth(), request, response);
            } else {
                try {
                    Authentication authentication = jwtTokenProvider.getAuthentication(token);
                    setAuthenticationToContext(authentication, request, response);
                } catch (BusinessException e) {
                    String errorMessage = buildErrorMessageForFilter(e);
                    writeErrorResponse(
                            response,
                            e.getExceptionStatus().getHttpStatus().value(),
                            e.getExceptionStatus().getCode(),
                            errorMessage);
                    return;
                } catch (JwtException e) {
                    writeErrorResponse(
                            response,
                            HttpServletResponse.SC_BAD_REQUEST,
                            "JWT_ERROR",
                            "JWT error: " + e.getMessage());
                    return;
                }
            }
            filterChain.doFilter(request, response);
        } catch (Exception e) {
            log.error("Unexpected error in authentication filter", e);
            writeErrorResponse(
                    response,
                    HttpServletResponse.SC_INTERNAL_SERVER_ERROR,
                    "INTERNAL_SERVER_ERROR",
                    "Internal server error occurred");
        }
    }

    private String buildErrorMessageForFilter(BusinessException ex) {
        String[] args = ex.getArgs();
        return (args != null && args.length > 0)
                ? Arrays.toString(args) + " " + ex.getMessage()
                : ex.getMessage();
    }

    private void writeErrorResponse(
            HttpServletResponse response, int status, String code, String message)
            throws IOException {
        response.setStatus(status);
        response.setContentType("application/json;charset=UTF-8");
        String errorJson =
                objectMapper.writeValueAsString(new ErrorResponse(status, code, message));
        response.getWriter().write(errorJson);
    }

    @Getter
    @AllArgsConstructor
    private static class ErrorResponse {
        private int status;
        private String code;
        private String message;
    }
```

