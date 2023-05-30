# JUC面试题

## 对AQS的理解

### AQS共享资源的方式：独占式和共享式

AQS 定义了两种资源共享方式 ：独占式 (Exclusive)和共享式(Share)

- **独占模式(Exclusive)**是只有一个线程能够访问共享资源，如 `ReentrantLock`。==又可分为**公平锁**和**非公平锁**（共享模式好像也分公平锁？非公平锁？）==：

    - **公平锁**：按照线程在队列中的排队顺序，先到者先拿到锁；
    - **非公平锁**：当线程要获取锁时，无视队列顺序直接去抢锁，谁抢到就是谁的。

- **共享模式(Share)**允许多个线程同时访问共享资源，如 `Semaphore`、`CountDownLatch`等。

    另外，`ReentrantReadWriteLock` 可以看成是**组合式**，即既包括共享式的`读锁`，又包括独占式的`写锁`。



### AQS使用了哪些设计模式？

​		AQS的设计是基于**模板方法模式**的。如果需要自定义同步器，一般的步骤是这样（模板方法模式很经典的一个应用）：

- 使用者继承`AbstractQueuedSynchronizer`并重写指定的方法。（所需重写的方法很简单，无非是对于共享资源state的获取和释放）
- 将AQS组合在自定义同步组件的实现中，并调用其模板方法，而这些模板方法会调用使用者重写的方法。

> ​		可以看到，这和我们以往通过实现接口的方式有很大区别，这是模板方法模式很经典的一个运用。



​		AQS是一个同步器框架，只是一个`抽象父类`，具体资源的获取、释放都由**自定义同步器**去实现。不同的自定义同步器争用共享资源的方式也不同，自定义同步器在实现时只需实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护，如获取资源失败入队、唤醒出队等， AQS 已经在顶层实现好，不需要具体的同步器再做处理。

​		自定义同步器时需要被重写的主要方法如下表所示：

| 序号 | 方法名            | 资源共享方式 | 说明                                                         |
| ---- | ----------------- | ------------ | ------------------------------------------------------------ |
| 1    | isHeldExclusively |              | 查询该线程是否正在独占资源，只有用到 condition 时，才需要去实现它 |
| 2    | tryAcquire        | 独占方式     | 尝试获取资源：成功则返回 true，失败则返回 false              |
| 3    | tryRelease        | 独占方式     | 尝试释放资源：成功则返回 true，失败则返回 false              |
| 4    | tryAcquireShared  | 共享方式     | 尝试获取资源：负数表示失败；0 表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源 |
| 5    | tryReleaseShared  | 共享方式     | 尝试释放资源：如果释放资源后允许唤醒后续等待线程，则返回 true，否则返回 false |

​		同步器的实现是 AQS 的核心内存。 ReentrantLock对AQS的独占方式实现为：ReentrantLock中的state初始值为0表示无锁状态。在线程执行 tryAcquire()获取该锁后ReentrantLock中的`state + 1`，这时该线程独占ReentrantLock锁，其他线程在通过`tryAcquire()` 获取锁时均会失败，直到该线程释放锁后，state再次为0，其他线程才有机会获取该锁。该线程在释放锁之前可以重复获取此锁，每获取一次便会执行一次state+1, 因此ReentrantLock也属于可重入锁。 但获取多少次锁就要释放多少次锁，这样才能保证state最终为0。如果获取锁的次数多于释放锁的次数，则会出现该线程一直持有该锁的情况；如果获取锁的次数少于释放锁的次数，则运行中的程序会报锁异常。

​		CountDownLatch对AQS的共享方式实现为：CountDownLatch 将任务分为N个子线程去执行，将 state 初始化为 N，N与线程的个数一致，N个子线程是井行执行的，每个子线程都在执行完成后 `countDown()`1次， state 执行 CAS 操作并减1。在所有子线程都执行完成( state=0)时会`unpark()`主线程，然后主线程会从 `await()`返回，继续执行后续的动作。

​		一般来说，自定义同步器要么采用独占方式，要么采用共享方式 ，实现类只需实现tryAcquire、tryRelease、tryAcquireShared、tryReleaseShared 中的一组即可。但AQS也支持自定义同步器同时实现独占和共享两种方式，例如 ReentrantReadWriteLock 在读取时采用了共享方式，在写入时采用了独占方式。



### AQS框架和Monitor框架像不像？

​		`AQS`和`Monitor`本质上都是基于`管程`实现。Monitor的底层是由两个队列：`EntryList` 和`waitSet`队列，一个计数器`recursion`（计数器就和AQS的state一样）和一个用于保存锁的所有者的`owner`组成。

​		AQS底层也是由两个队列：`LCH同步队列`和`condition队列`、state、ownerThread组成。



**Condition 和 AQS 有什么关系？**

​		Condition是基于AQS实现的，Condition的出现有利于线程之间的协作。



**Condition 的实现原理是什么？**

​		Condition 内部维护一个**条件队列**，在获取锁的情况下，线程调用 `await`，线程会被放置在条件队列中并被阻塞，直到调用 `signal`、`signalAll` 唤醒线程。当线程被唤醒之后，会放入到 AQS 的**同步队列**，参与争抢锁资源。



**Condition 的等待队列和 AQS 的同步队列有什么区别和联系？**

​		Condition 的等待队列是**单向链表**，AQS 的是**双向链表**。二者之间并没有什么明确的联系，仅仅在节点从阻塞状态被唤醒后，会从等待队列挪到同步队列中（**这里会根据是公平的锁还是非公平锁来决定是否插队**）。



**Condition.singal() & await()和Object.wait & notify有什么区别？**

​		Object.wait notify必须和synchronized关键字一起使用，并且只能唤醒一个（notify）或者唤醒全部（notifyall）。而Condition.singal() await()可以对不同的等待队列进行控制。



### Condition.await()和Condition.singal()实现原理。

​		`Condition.await()`和`Condition.singal()`与`Object.wait()`和`Object.notify()`方法类似，都是用来**进行线程之间的协作**。ReentrantLock为condition创建了一个**<font color="red">condition队列</font>**，将调用了`await`的线程，添加到condition队列中。当某个线程调用Condition.singal()时，会从`condition队列`中取出线程并放入到`同步队列`，参与争抢锁资源【公平 VS 公平，都是放入同步队列之后再才能争抢锁资源么】。



### Condition.await()的源码

> ​		其实就是将调用await的线程，以及waitStatus，封装成一个Node节点，然后加入condition队列中。

```java
public final void await() throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    
    Node node = addConditionWaiter();
    int savedState = fullyRelease(node);
    int interruptMode = 0;
    while (!isOnSyncQueue(node)) {
        LockSupport.park(this);
        if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
            break;
    }
    if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
        interruptMode = REINTERRUPT;
    if (node.nextWaiter != null) // clean up if cancelled
        unlinkCancelledWaiters();
    if (interruptMode != 0)
        reportInterruptAfterWait(interruptMode);
}

private Node addConditionWaiter() {
    Node t = lastWaiter;
    if (t != null && t.waitStatus != Node.CONDITION) {
        unlinkCancelledWaiters();
        t = lastWaiter;
    }
    Node node = new Node(Thread.currentThread(), Node.CONDITION);
    if (t == null)
        firstWaiter = node;
    else
        t.nextWaiter = node;
    lastWaiter = node;
    return node;
}
```



### Condition.singal()的源码

> ​		调用 Condition.singal() 会将condition队列中的线程加入到等待队列中参与锁资源的竞争（是非公平锁还是公平锁）。

```java
public final void signal() {
    if (!isHeldExclusively())
        throw new IllegalMonitorStateException();
    Node first = firstWaiter;
    if (first != null)
        doSignal(first);
}

private void doSignal(Node first) {
    do {
        if ( (firstWaiter = first.nextWaiter) == null)
            lastWaiter = null;
        first.nextWaiter = null;
    } while (!transferForSignal(first) &&
             (first = firstWaiter) != null);
}

final boolean transferForSignal(Node node) {
    if (!compareAndSetWaitStatus(node, Node.CONDITION, 0))
        return false;

    Node p = enq(node);
    int ws = p.waitStatus;
    if (ws > 0 || !compareAndSetWaitStatus(p, ws, Node.SIGNAL))
        LockSupport.unpark(node.thread);
    return true;
}

/**
 * Inserts node into queue, initializing if necessary. See picture above.
 * @param node the node to insert
 * @return node's predecessor
 */
private Node enq(final Node node) {
    for (;;) {
        Node t = tail;
        if (t == null) { // Must initialize
            if (compareAndSetHead(new Node()))
                tail = head;
        } else {
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```





























