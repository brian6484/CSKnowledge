## Scenario
Let's suppose we are authenticated and logged into our accounts. An attacker can make a malicious request and embed that into a link or a script
and make us execute that request. Since we are authenticated, the website doesn't suspect anything and does it.

## CSRF (Cross-Site Request Forgery)
That is an example of CSRF. There are several solutions like making a CSRF token or including a custom header like 'X-requested-with'. 

## Spring Security
Spring Boot's Security automatically checks for this CSRF by making sure that any HTTP request must come with a correct CSRF token. However,
if we are using JWT token, which is stateless, that can and should be turned off
