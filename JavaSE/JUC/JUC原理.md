# JUC原理

## 一、AQS 原理

[(112条消息) 什么是AQS_阿德小仔的博客-CSDN博客](https://blog.csdn.net/qq_40322236/article/details/127254744)

### 1.1 概述

AQS的全称是 `AbstractQueuedSynchronizer`。

- 从**实现原理**来谈：AQS是一个抽象的队列同步器，通过维护一个`共享资源状态(volatile Int State)`和一个`先进先出的线程等待队列`来实现一个**<font color="red">多线程访问共享资源的同步框架</font>**。
- 从**代码实现**来谈：AQS是阻塞式锁和相关的同步器工具的框架，是一个抽象父类。



### 1.2 原理











- 用 state 属性来表示资源的状态（分独占模式和共享模式），子类需要定义如何维护这个状态，控制如何获取锁和释放锁
    - getState - 获取 state 状态
    - setState - 设置 state 状态（适用于单线程环境或者在线程释放锁时）
    - compareAndSetState - cas 机制设置 state 状态（保证多线程下并发修改state的线程安全）
    - 独占模式是只有一个线程能够访问资源，而共享模式可以允许多个线程访问资源
- 提供了基于 FIFO 的等待队列，类似于 Monitor 的 EntryList
- 条件变量来实现等待、唤醒机制，支持多个条件变量，类似于 Monitor 的 WaitSet

子类主要实现这样一些方法（默认抛出 UnsupportedOperationException）

- tryAcquire
- tryRelease
- tryAcquireShared
- tryReleaseShared
- isHeldExclusively



```java
// 获取锁的姿势
// 如果获取锁失败
if (!tryAcquire(arg)) {
    // 入队, 可以选择阻塞当前线程 park unpark
}

// 释放锁的姿势
// 如果释放锁成功
if (tryRelease(arg)) {
    // 让阻塞线程恢复运行
}
```



### 3、自定义锁

​		基于 AQS 实现一个`独占式`的、`不可重入`的**阻塞式锁**：使用 `AbstractQueuedSynchronizer` 自定义一个同步器来实现自定义锁，代码如下：

```java
/**
 * @description 基于AQS实现自定义锁：独占、不可重入锁
 * @author Jun Lu
 * @date 2023-04-22 09:43:03
 */
public class AQSCustomLock implements Lock {

    /**
     * @description 加锁状态枚举类
     * @author Jun Lu
     * @date 2023-04-22 09:51:32
     */
    enum LockState {
        NORMAL("无锁状态", 0),
        LOCK("被加锁状态", 1)
            ;

        private final String lockState;
        private final int state;

        LockState(String lockState, int state) {
            this.lockState = lockState;
            this.state = state;
        }

        public String getLockState() {
            return lockState;
        }

        public int getState() {
            return state;
        }

        @Override
        public String toString() {
            return "LockState{" +
                "lockState='" + lockState + '\'' +
                ", state=" + state +
                '}';
        }
    }

    /**
     * @description 自定义同步器类：目的是实现独占锁
     * @author Jun Lu
     * @date 2023-04-22 09:42:52
     */
    class MyAnsynchronizer extends AbstractQueuedSynchronizer {

        @Override
        // 尝试获取锁
        protected boolean tryAcquire(int arg) {
            if (compareAndSetState(LockState.NORMAL.getState(), LockState.LOCK.getState())) {
                // 若加上了锁，则设置“独占锁所有者”为当前线程
                setExclusiveOwnerThread(Thread.currentThread());
                return true;
            }
            return false;
        }

        @Override
        // 释放锁
        protected boolean tryRelease(int arg) {
            try {
                // 解锁逻辑：设置“独占锁所有者”为null
                setExclusiveOwnerThread(null);
                // 由于state变量是volatile的，则能保证有序性。
                // 所以，应该写在setExclusiveOwnerThread()方法之后，充分发挥写屏障的作用
                setState(LockState.NORMAL.getState());
                return true;
            } catch (Exception e) {   // 出现异常，解锁失败
                e.printStackTrace();
                return false;
            }
        }

        @Override
        // 当前线程是否持有锁
        protected boolean isHeldExclusively() {
            return getState() == LockState.LOCK.getState();
        }

        public Condition newCondition() {
            return new ConditionObject();
        }
    }

    private final MyAnsynchronizer sync = new MyAnsynchronizer();

    @Override
    // 加锁不成功会进入阻塞队列，直到获取到锁为止
    public void lock() {
        // 当前的加锁逻辑是不能使用MyAnsynchronizer中重写的tryAcquire方法的
        // 因为tryAcquire是tryLock()方法的实现逻辑！
        sync.acquire(LockState.LOCK.getState());
    }

    @Override
    // 加锁，可被打断。若加锁不成功会进入阻塞队列，直到获取到锁为止
    public void lockInterruptibly() throws InterruptedException {
        sync.acquireInterruptibly(LockState.LOCK.getState());
    }

    @Override
    // 尝试加锁。加锁成功则返回true；加锁失败则返回false，而不会进入阻塞队列。
    public boolean tryLock() {
        try {
            sync.tryAcquire(LockState.LOCK.getState());
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    @Override
    // 尝试在给定的超时时间内加锁。加锁成功则返回true；加锁失败则返回false，而不会进入阻塞队列。
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        sync.tryAcquireNanos(LockState.LOCK.getState(), unit.toNanos(time));
    }

    @Override
    // 解锁
    public void unlock() {
        sync.release(LockState.NORMAL.getState());
    }

    @Override
    // 新建一个条件变量
    public Condition newCondition() {
        return sync.newCondition();
    }
}
```



- 测试类：测试是否可重入

```java
public static void main(String[] args) {

    AQSCustomLock myLock = new AQSCustomLock();
    new Thread(() -> {
        myLock.lock();
        log.info("lock ... ");
        // 测试是否可重入
        myLock.lock();
        try {
            log.info("starting ... ");
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            log.info("unlock ... ");
            myLock.unlock();
        }
    }, "t1").start();

}
```



































































