## AOP and Transactional pitfall
This was due to my shallow understanding of AOP and @Transactional.

## 1st pitfal
methods that have @Transactional or other AOP annotations **CANNOT BE PRIVATE**. This is cuz proxy classes **extend** the
target class. Basically this proxy object *wraps* around the original object and intercepts method calls, allowing Spring Boot to 
introduce additonal behaviour like transaction management/ AOP features.

But if the target class's method is private, it is not visible to other class. Since proxy class needs to **override** the methods
to allow this additional features, if method is private it is not visible to proxcy class so it won't be overriden. So it won't work.

So make it **public**!

## 2nd pitfall
Observe this code in a service bean:
```java
@RequireLock
public SettleableProfitDto withdrawSettleableProfit(SettleableProfitRequest settleableProfitRequest){
    return withdrawProfitAndLogForTransaction(settleableProfitRequest);
}

@Transactional
public SettleableProfitDto withdrawProfitAndLogForTransaction(SettleableProfitRequest settleableProfitRequest) {
    SettleableProfit settleableProfit = findAndLockSettleableProfit(settleableProfitRequest);

    long settleableProfitRequestAmount = settleableProfitRequest.getAmount();

    settleableProfitRepository.save(settleableProfit.subtractAmount(settleableProfitRequestAmount));
    profitSettlementLogService.create(settleableProfitRequestAmount,settleableProfit);
    return SettleableProfitDto.toDto(settleableProfit);
}
```

As you can see, we have created a custom annotation @RequireLock that works via AOP and we want to require lock first before withdrawing
and logging this particular transaction. So I thought oh just separate the order of execution via method extraction. BUT that was really
really dumb. This is because AOP actually works via [proxy, not the actual object instance](https://github.com/brian6484/CSKnowledge/blob/main/Backend/Spring/AOP/Spring%20AOP%20.md).

So if a method calls another class within the same class (self-invocation), it may bypass the proxy and directly reference the actual object
instance. As we know, AOP aspect works ONLY on proxies. So when a bean is proxied, a proxy object is created and this proxy object is
meant to **intercept method calls and execute aspect-related logic before/after method invocation**. But self-invocation deos not go
through this proxy, so aspects related to this proxy like **@Transactional** doesnt work properly.

## Solution
Declare the method in another Service bean (or EventTransactionHandler tbc)

```java
public class SettleableProfitService {
    private final SettleableProfitRepository settleableProfitRepository;

    private final SettleableProfitTransactionService settleableProfitTransactionService;

    @RequireLock
    public SettleableProfitDto withdrawSettleableProfit(SettleableProfitRequest settleableProfitRequest){
        return settleableProfitTransactionService.withdrawProfitAndLogForTransaction(settleableProfitRequest);
    }
```

and in that transactionservice bean,
```java
public class SettleableProfitTransactionService {

    @Transactional
    public SettleableProfitDto withdrawProfitAndLogForTransaction(SettleableProfitRequest settleableProfitRequest) {
        SettleableProfit settleableProfit = findAndLockSettleableProfit(settleableProfitRequest);

        long settleableProfitRequestAmount = settleableProfitRequest.getAmount();

        settleableProfitRepository.save(settleableProfit.subtractAmount(settleableProfitRequestAmount));
        profitSettlementLogService.create(settleableProfitRequestAmount,settleableProfit);
        return SettleableProfitDto.toDto(settleableProfit);
    }

    private SettleableProfit findAndLockSettleableProfit(SettleableProfitRequest settleableProfitRequest) {
        Optional<SettleableProfit> optionalSettleableProfit = settleableProfitRepository.findByUserId(settleableProfitRequest.getUserId());
        SettleableProfit settleableProfit = optionalSettleableProfit.orElseThrow(() -> new BusinessException(ExceptionStatus.SETTLEABLE_PROFIT_NOT_FOUND));
        return settleableProfit;
    }
```

This way, when we call another bean's method, this isnt self-invocation but calling external method, which proxies properly intercept this
invocation.
 
