# GoF设计模式

工厂模式的讲解请参考：[老杜Spring笔记：密码-mg9b](https://www.yuque.com/docs/share/866abad4-7106-45e7-afcd-245a733b073f?# ) 进行修正

## 初识设计模式

- 设计模式：一种可以被重复利用的解决方案。

- GoF（Gang of Four），中文名——四人组。
- 《Design Patterns: Elements of Reusable Object-Oriented Software》（即《设计模式》一书），1995年由 Erich Gamma、Richard Helm、Ralph Johnson 和 John Vlissides 合著。这几位作者常被称为"四人组（Gang of Four）"。
- 该书中描述了23种设计模式。我们平常所说的设计模式就是指这23种设计模式。
- 不过除了GoF总结的23种设计模式之外，还存在其它的设计模式，比如：JavaEE的设计模式（DAO模式、MVC模式等）。
- GoF23种设计模式可分为三大类：

- - **创建型**（5个）：解决对象创建问题。

- - - 单例模式
    - 工厂方法模式
    - 抽象工厂模式
    - 建造者模式
    - 原型模式

- - **结构型**（7个）：一些类或对象组合在一起的经典结构。

- - - 代理模式
    - 装饰模式
    - 适配器模式
    - 组合模式
    - 享元模式
    - 外观模式
    - 桥接模式

- - **行为型**（11个）：解决类或对象之间的交互问题。

- - - 策略模式
    - 模板方法模式
    - 责任链模式
    - 观察者模式
    - 迭代子模式
    - 命令模式
    - 备忘录模式
    - 状态模式
    - 访问者模式
    - 中介者模式
    - 解释器模式



## 一、工厂模式

### 1. 工厂模式通常有三种形态

- 第一种：**简单工厂模式（Simple Factory）：不属于GoF 23种设计模式之一。简单工厂模式又叫做：静态工厂方法模式。简单工厂模式是工厂方法模式的一种特殊实现。**
- 第二种：工厂方法模式（Factory Method）：是23种设计模式之一。
- 第三种：抽象工厂模式（Abstract Factory）：是23种设计模式之一。



### 2. 简单工厂模式

#### 1）简单工厂模式中的角色

- `抽象产品`角色
- `具体产品`角色
- `工厂类`角色



#### 2）简单工厂模式案例实现

- 抽象产品角色

```java
/**
 * 武器抽象类（抽象产品角色）
 **/
public abstract class Weapon {
    /**
     * 所有的武器都有攻击行为
     */
    public abstract void attack();
}
```



- 具体产品角色

```java
/**
 * 坦克类（具体产品角色）
 **/
public class Tank extends Weapon{
    @Override
    public void attack() {
        System.out.println("坦克开炮！");
    }
}

// =================================================================
/**
 * 战斗机类（具体产品角色）
 **/
public class Fighter extends Weapon{
    @Override
    public void attack() {
        System.out.println("战斗机投下原子弹！");
    }
}

// =================================================================
/**
 * 匕首类（具体产品角色）
 **/
public class Dagger extends Weapon{
    @Override
    public void attack() {
        System.out.println("砍他丫的！");
    }
}
```



- 工厂类角色

```java
/**
 * 工厂类角色
 **/
public class WeaponFactory {
    /**
     * 根据不同的武器类型生产武器
     * @param weaponType 武器类型
     * @return 武器对象
     */
    public static Weapon get(String weaponType){
        if (weaponType == null || weaponType.trim().length() == 0) {
            return null;
        }
        Weapon weapon = null;
        if ("TANK".equals(weaponType)) {
            weapon = new Tank();
        } else if ("FIGHTER".equals(weaponType)) {
            weapon = new Fighter();
        } else if ("DAGGER".equals(weaponType)) {
            weapon = new Dagger();
        } else {
            throw new RuntimeException("不支持该武器！");
        }
        return weapon;
    }
}
```





#### 3）简单工厂模式的优缺点

- 简单工厂模式的优点
  - 客户端程序不需要关心对象的创建细节，需要哪个对象时，只需要向工厂索要即可，初步实现了责任的分离。客户端只负责“消费”，工厂负责“生产”。生产和消费分离。

- 简单工厂模式的缺点
  - 缺点1：工厂类集中了所有产品的创造逻辑，形成一个无所不知的全能类，有人把它叫做上帝类。显然工厂类非常关键，不能出问题，一旦出问题，整个系统瘫痪。
  - 缺点2：不符合OCP开闭原则，在进行系统扩展时，需要修改工厂类。

**Spring中的BeanFactory就使用了简单工厂模式。**



















