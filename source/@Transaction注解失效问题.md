@Transaction注解失效问题



当Service层使用事务注解时,方法中trycatch会导致注解失效,因此当捕获异常时要手动回滚事务,在catch中写

```java
TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
```

