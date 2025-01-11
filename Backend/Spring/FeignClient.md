## How backend communication works
Lets consider a case where I need to get an access token from an external API .
Normally, I am used to using RestTemplate for this but there is this FeignClient which is much simpler.

## Adv
1) You just define an interface that describes the API endpoints. FeignClient will generate the necessary HTTP request code at **runtime**.
2) Reduced boilerplate code cuz you dont need to create HttpEntity and manually handle the response objects.

## Example
ResTemplate
```java
RestTemplate restTemplate = new RestTemplate();
String url = "https://internal-api.example.com/token";
HttpHeaders headers = new HttpHeaders();
headers.set("Authorization", "Bearer " + token);
HttpEntity<String> entity = new HttpEntity<>(headers);

ResponseEntity<String> response = restTemplate.exchange(url, HttpMethod.GET, entity, String.class);
```

FeignClient (literally just an interface)
```java
@FeignClient(name = "InternalApiClient", url = "https://internal-api.example.com")
public interface InternalApiClient {
    @GetMapping("/token")
    String getToken(@RequestHeader("Authorization") String token);
}

// Usage
String token = internalApiClient.getToken("Bearer " + token);
```

## Ref
https://mjoo1106.tistory.com/entry/Spring-FeignClient-%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C
