## Java's Optional
I will arrange the notes as seen in this [link](https://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/)
but I have been wrongly using Optional against its intended purpose.

```java
Optional<SettleableProfit> optionalSettleableProfit = settleableProfitRepository.findByUser(user);
if (optionalSettleableProfit.isEmpty()) {
    throw new BusinessException(ExceptionStatus.SETTLEABLE_PROFIT_NOT_FOUND);
}
SettleableProfit sp = optionalSettleableProfit.get();
```

This isEmpty() and get() introduces additional boilerplate code that is not expressive.

Instead, use .orElse() or .orElseThrow() for exceptions
```java
SettleableProfit settleableProfit = optionalSettleableProfit.orElseThrow(() -> new BusinessException(ExceptionStatus.SETTLEABLE_PROFIT_NOT_FOUND));
```
