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

## @JsonProperty to map json field to object field
If the names of the fields dont match, we have to use @JsonProperty like this. It is like @SerializedName in [gson](https://github.com/brian6484/CSKnowledge/blob/main/Language/Java/File/Gson.md).

```java
public record Order(@JsonProperty("order_id") String orderId,
                    @JsonProperty("order_status") String orderStatus,
                    Delivery delivery,
                    int amount) {
}
```
