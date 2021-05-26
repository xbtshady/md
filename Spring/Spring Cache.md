## Spring Cache

基于凝视（annotation）的缓存（cache）技术。

```java
@Cacheable(value="accountCache")
public Account getAccountByName(String accountName) {
    // 方法内部实现不考虑缓存逻辑，直接实现业务
    logger.info("real querying account... {}", accountName);
    Optional<Account> accountOptional = getFromDB(accountName);
    if (!accountOptional.isPresent()) {
        throw new IllegalStateException(String.format("can not find account by account name : [%s]", accountName));
    }
    return accountOptional.get();
}
```

这个凝视的意思是，当调用这种方法的时候。会从一个名叫 accountCache 的缓存中查询，假设没有，则运行实际的方法（即查询数据库），并将运行的结果存入缓存中。否则返回缓存中的对象。这里的缓存中的 key 就是參数 accountName，value 就是 Account 对象。java