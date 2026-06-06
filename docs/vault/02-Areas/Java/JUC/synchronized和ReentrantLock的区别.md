
1. 用法不同。synchronized可以修饰普通方法，静态方法，代码块；而ReentrantLock只能用在代码块
2. 加锁和释放锁方式不同。synchronized自动加锁解锁，ReentrantLock需要手动加锁解锁
3. 锁类型不同。synchronized是非公平锁，ReentrantLock可以是公平锁也可以是非公平锁，可以构造时指定
4. 响应中断不同。synchronized无法设置超时和手动中断，会产生死锁，；ReentrantLock获取锁可以设置超时，可以手动中断
5. 底层实现不同。synchronized是JVM层面通过Monitor监视器实现(monitorenter monitorexit);ReentrantLock是通过AQS(AbstractQueuedSynchronizer)程序级别的API实现

相同：都是可重入锁