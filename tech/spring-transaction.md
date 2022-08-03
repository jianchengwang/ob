---
title: spring-transaction
createdAt: 2022-08-03
categories: 
- spring
tags: 
- spiring
- transaction
---

最近瞄了下spring的事务机制，这里简单做个记录

## @Transaction 声明式事务
在应用系统调用声明了 @Transactional 的目标方法时，Spring Framework 默认使用 AOP 代理，在代码运行时生成一个代理对象，根据 @Transactional 的属性配置信息，这个代理对象决定该声明 @Transactional 的目标方法是否由拦截器 TransactionInterceptor 来使用拦截，在 TransactionInterceptor 拦截时，会在目标方法开始执行之前创建并加入事务，并执行目标方法的逻辑, 最后根据执行情况是否出现异常，利用抽象事务管理器 AbstractPlatformTransactionManager 操作数据源 DataSource 提交或回滚事务。

Spring AOP 代理有 CglibAopProxy 和 JdkDynamicAopProxy 两种，以 CglibAopProxy 为例，对于 CglibAopProxy，需要调用其内部类的 DynamicAdvisedInterceptor 的 intercept 方法。对于 JdkDynamicAopProxy，需要调用其 invoke 方法。

正如上文提到的，事务管理的框架是由抽象事务管理器 AbstractPlatformTransactionManager 来提供的，而具体的底层事务处理实现，由 PlatformTransactionManager 的具体实现类来实现，如事务管理器 DataSourceTransactionManager。不同的事务管理器管理不同的数据资源 DataSource，比如 DataSourceTransactionManager 管理 JDBC 的 Connection。

### 明确几点
1. 默认配置下 Spring 只会回滚运行时、未检查异常（继承自 RuntimeException 的异常）或者 Error。参考这里
2. @Transactional 注解只能应用到 public 方法才有效。参考这里 Method visibility and @Transactional
3. 在默认的代理模式下，只有目标方法由外部调用，才能被 Spring 的事务拦截器拦截。在同一个类中的两个方法直接调用，是不会被 Spring 的事务拦截器拦截，就像上面的 save 方法直接调用了同一个类中的 method1方法，method1 方法不会被 Spring 的事务拦截器拦截。可以使用 AspectJ 取代 Spring AOP 代理来解决这个问题，但是这里暂不讨论。

下面介绍一下注解的几个属性

### propagation 属性
事务的传播行为，默认值为 Propagation.REQUIRED。
可选的值有：
1. Propagation.REQUIRED
如果当前存在事务，则加入该事务，如果当前不存在事务，则创建一个新的事务。
2. Propagation.SUPPORTS
如果当前存在事务，则加入该事务；如果当前不存在事务，则以非事务的方式继续运行。
3. Propagation.MANDATORY
如果当前存在事务，则加入该事务；如果当前不存在事务，则抛出异常。
4. Propagation.REQUIRES_NEW
重新创建一个新的事务，如果当前存在事务，暂停当前的事务。
5. Propagation.NOT_SUPPORTED
以非事务的方式运行，如果当前存在事务，暂停当前的事务。
6. Propagation.NEVER
以非事务的方式运行，如果当前存在事务，则抛出异常。
7. Propagation.NESTED
和 Propagation.REQUIRED 效果一样。

### isolation 属性
事务的隔离级别，默认值为 Isolation.DEFAULT。
1. Isolation.DEFAULT
    使用底层数据库默认的隔离级别。
2. Isolation.READ_UNCOMMITTED
3. Isolation.READ_COMMITTED
4. Isolation.REPEATABLE_READ
5. Isolation.SERIALIZABLE

### timeout 属性
事务的超时时间，默认值为-1。如果超过该时间限制但事务还没有完成，则自动回滚事务。

### readOnly 属性
指定事务是否为只读事务，默认值为 false；为了忽略那些不需要事务的方法，比如读取数据，可以设置 read-only 为 true。

### rollbackFor 属性
用于指定能够触发事务回滚的异常类型，可以指定多个异常类型。

### noRollbackFor 属性
抛出指定的异常类型，不回滚事务，也可以指定多个异常类型。
