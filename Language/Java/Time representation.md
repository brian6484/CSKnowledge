## Don't use string to represent time in Java!
wrong
```java
public record DeliveryResponse(@JsonProperty("delivery_id") String deliveryId,
 @JsonProperty("delivery_time") String deliveryTime)

```

## Complications
If the string does not include a time zone or offset (e.g., 2024-11-22T14:30 instead of 2024-11-22T14:30+00:00), it's unclear whether 
the time is in UTC, local time, or some other zone. This can cause issues when performing time calculations or converting between time zones.

Also, a raw string doesnt have a specific format so it can be yyyy-mm-dd or dd-mm-yyyy.

## Use Instant or ZonedDateTime
Instant: Represents an exact moment in time (UTC-based), suitable if no time zone information is required.

ZonedDateTime: Includes date, time, and time zone information, making it ideal for scenarios involving multiple time zones.

They both ensure consistent parsing and format as well as setting a specific time zone.
