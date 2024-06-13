## Jackson
Similar to Gson, it is .writeValueAsString() for serialisation and .readValue() for deserialisation. Serialisation is object to json.

## object -> json with .writeValueAsString()
```java
ObjectMapper mapper = new ObjectMapper(); 
String jsonResult = mapper.writeValueAsString(json으로 바꾸고싶은 java객체);
```

## json -> object with readValue()
```java
String jsonInput = "json 데이터";
ObjectMapper mapper = new ObjectMapper();
Example exam = mapper.readValue(jsonInput, Example.class);
```
