## Java's Optional
I have arranged the notes as seen in this [link](https://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/)
but I have been wrongly using Optional against its intended purpose.

So according to the creator of Optional, [Brian](https://stackoverflow.com/questions/26327957/should-java-8-getters-return-optional-type/26328555#26328555) said you should NEVER use .get() unless you are SURE that the value is NOT NULL. The original purpose of Optional
is not a Maybe type where oh if there isnt a value in that, we throw an exception or if there is indeed a value we get that value via
get(). Its original purpose is as written:

```
Optional is primarily intended for use as a method return type where there is a clear need to represent “no result,” and where using null is likely to cause errors. 
```

Brian Goetz advises against using get() unless you are absolutely certain that the value is present. Instead, he suggests using methods like ifPresent, orElse, or orElseGet to safely handle both cases without risking a NoSuchElementException.

## Example 
wrong use:
```java
Optional<SettleableProfit> optionalSettleableProfit = settleableProfitRepository.findByUser(user);
if (optionalSettleableProfit.isEmpty()) {
    throw new BusinessException(ExceptionStatus.SETTLEABLE_PROFIT_NOT_FOUND);
}
SettleableProfit sp = optionalSettleableProfit.get();
```

This isEmpty() and get() introduces additional boilerplate code that is not expressive and especially this get() is not recommended as mentioned.

Instead, use .orElse() or .orElseThrow() or .ifPresent() for exceptions. This .ifPresent() is opposite of orElse cuz unlike OrElse where if there isnt an entity present we deal with that situation, ifPresent is when there **is** an entity found and we deal with that situation. For example, when creating account, if there is already a duplicate username found in DB via findByUsername(), then we use .ifPresent() and throw an exception

correct:
```java
Optional<Member> member = ...;
return member.orElse(null);

SettleableProfit settleableProfit = optionalSettleableProfit.orElseThrow(() -> new BusinessException(ExceptionStatus.SETTLEABLE_PROFIT_NOT_FOUND));
```

## orElse(new ...) vs orElse(() -> new ... )
Lets take a general example first. If we have method1(method2()), of course method 2 runs first before method1. So in orElse(new ...),
here too this new ... will be executed first, **REGARDLESS of if there is a value in that optional instance or not**. So if we are creating
an instance or computing something in those brackets, we should use **orElse(() -> new ... ) or orElseGet(() -> new ... )**

this supplier in orElseGet(Supplier) will only be executed if there is no value in Optional so there is no unnecessary overhead (extra
resources used consumed by a task that doesnt contribute to the task's primary goal) caused.

## ifPresentOrElse()
Initial inefficient code
```java
private void aggregateByNotificationType(
            List<Notification> oldNotifications, NotificationType type) {
        oldNotifications.stream()
                .collect(Collectors.groupingBy(Notification::getPost)) // post 별로 그룹화
                .forEach(
                        (post, notifications) -> {
                            AggregateNotification oldAggregatedNotification =
                                    aggregateNotificationRepository.findByPost(post).orElse(null);

                            if (oldAggregatedNotification != null) {
                                Long count =
                                        oldAggregatedNotification.getCount() + notifications.size();
                                AggregateNotification updatedNotification =
                                        oldAggregatedNotification.updateAggregateNotification(
                                                count);
                                aggregateNotificationRepository.save(updatedNotification);
                            } else {
                                AggregateNotification newAggregatedNotification =
                                        AggregateNotification.createAggregateNotification(
                                                post.getUser(),
                                                post,
                                                type,
                                                (long) notifications.size());
                                aggregateNotificationRepository.save(newAggregatedNotification);
                            }
```
Lets say we are in a stream and forEach element, we do some logic that depends on a computation of an intermediate operation. If that is null, we call a function and if not, we call another function. This is what the initial code is doing. We findByPost and if oldAggregatedNotification is not null we createAggregateNotification and else, we updateAggregateNotification.

Using ifPresentOrElse, this is much more efficient and clean. Like this
```java
    private void processPostNotifications(Post post, List<Notification> notifications) {
        NotificationType type = notifications.get(0).getType();
        User user = post.getUser();
        long count = notifications.size();
        
        // Find existing aggregate notification or create new one
        aggregateNotificationRepository.findByPost(post)
                .ifPresentOrElse(
                    // Update existing aggregate
                    existingAggregate -> updateAggregateNotification(existingAggregate, count),
                    // Create new aggregate
                    () -> createAggregateNotification(user, post, type, count)
                );
                
        // Delete processed notifications
        notificationRepository.deleteAll(notifications);
    }
    
    private void updateAggregateNotification(AggregateNotification aggregateNotification, long additionalCount) {
        Long newCount = aggregateNotification.getCount() + additionalCount;
        AggregateNotification updatedNotification = aggregateNotification.updateAggregateNotification(newCount);
        aggregateNotificationRepository.save(updatedNotification);
    }
    
    private void createAggregateNotification(User user, Post post, NotificationType type, long count) {
        AggregateNotification newAggregate = AggregateNotification.createAggregateNotification(
                user, post, type, count);
        aggregateNotificationRepository.save(newAggregate);
    }
```
