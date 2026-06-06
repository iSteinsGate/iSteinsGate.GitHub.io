
事务传播机制`Propagation

|定义|说明|
|---|---|
|PROPAGATION_REQUIRED（`默认`）|如果当前没有事务，就新建一个事务，如果已经存在一个事务，则加入到这个事务中。这是最常见的选择。|
|PROPAGATION_SUPPORTS|支持当前事务，如果当前没有事务，就以非事务方式执行。|
|PROPAGATION_MANDATORY|表示该方法必须在事务中运行，如果当前事务不存在，则会抛出一个异常。|
|PROPAGATION_REQUIRED_NEW|表示当前方法必须运行在它自己的事务中。一个新的事务将被启动。如果存在当前事务，在该方法执行期间，当前事务会被挂起。|
|PROPAGATION_NOT_SUPPORTED|表示该方法不应该运行在事务中。如果当前存在事务，就把当前事务挂起。|
|PROPAGATION_NEVER|表示当前方法不应该运行在事务上下文中。如果当前正有一个事务在运行，则会抛出异常。|
|PROPAGATION_NESTED|如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。|



关于事务嵌套导致Transaction rolled back because it has been marked as rollback-only问题

出现原因：在ClassA类中有个加了事务的A方法，调用了ClassB中的加了事务的B方法
```java
public Class ClassA{  
    private ClassB classB;  
    @Transactional 
    public void A () {  
	    try {  
	        B();  
	    } catch (Exception e) {  
	        log.error("啥也不干");  
	    }  
	}  
}  
public Class ClassB  {  
    @Transactional 
    public void B () {  
    throw new Exception();  
	}  
}

```

解决办法
1. 在内层事务中try...catch即可
2. 如果希望B内层事务回滚而不影响外层事务提交，可以把b方法的事务传播机制设置为：`@Transactional(propagation = Propagation.NESTED)`
3. 外层不用try..catch或者外层抛出异常