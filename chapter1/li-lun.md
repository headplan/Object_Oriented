# 封装 , 抽象 , 继承 , 多态分别可以解决哪些编程问题 ?

#### 封装\(Encapsulation\)

**封装**也叫信息隐藏或者数据访问保护 . 类通过暴露有限的访问接口 , 授权外部仅能通过类提供的方式\(或者叫函数\)来访问内部信息或者数据 .

下面是一个简化的虚拟钱包代码实现 :

```java
public class Wallet {
    private String id; // 钱包的唯一编号
    private long createTime; // 钱包的创建时间
    private BigDecimal balance; // 钱包中的余额
    private long balanceLastModifiedTime; // 上次钱包余额变更的时间

    // 初始化
    public Wallet() {
        this.id = IdGenerator.getInstance().generate();
        this.createTime = System.currentTimeMillis();
        this.balance = BigDecimal.ZERO;
        this.balanceLastModifiedTime = System.currentTimeMillis();
    }

    // get获取属性
    public String getId() { return this.id; }
    public long getCreateTime() { return this.createTime; }
    public BigDecimal getBalance() { return this.balance; }
    public long getBalanceLastModifiedTime() { return this.balanceLastModifiedTime; }

    public void increaseBalance(BigDecimal increasedAmount) {
        if (increasedAmount.compareTo(BigDecimal.ZERO) < 0) {
          throw new InvalidAmountException("...");
        }
        this.balance.add(increasedAmount);
        this.balanceLastModifiedTime = System.currentTimeMillis();
    }

    public void decreaseBalance(BigDecimal decreasedAmount) {
        if (decreasedAmount.compareTo(BigDecimal.ZERO) < 0) {
          throw new InvalidAmountException("...");
        }
        if (decreasedAmount.compareTo(this.balance) > 0) {
          throw new InsufficientAmountException("...");
        }
        this.balance.subtract(decreasedAmount);
        this.balanceLastModifiedTime = System.currentTimeMillis();
    }
}
```

Wallet类主要有四个属性 . 参照封装特性 , 对钱包的这四个属性的访问方式进行了限制 . 只允许调用通过下面六个方法来访问或者修改钱包里的数据 .

* String getId\(\)
* long getCreateTime\(\)
* BigDecimal getBalance\(\)
* long getBalanceLastModifiedTime\(\)
* void increaseBalance\(BigDecimal increasedAmount\)
* void decreaseBalance\(BigDecimal decreasedAmount\)

从业务的角度来说 , id , createTime在创建钱包的时候就确定好了 , 之后不应该再被改动 . 所以在Wallet类中没有暴露任何修改这两个属性的方法 set都没有 . 这两个属性的初始化设置 , 对于Wallet类的调用者来说 , 应该也是透明的 . 这里在构造函数内将其初始化设置好了 , 而不是通过构造函数的参数来外部赋值 .

对于balance属性 , 也就是钱包中的余额 . 只能增减 , 不存在重新设置 . 所以 , 这里只暴露了两个方法 , increaseBalance\(\)和decreaseBalance\(\) , 也没有暴露set方法 . balanceLastModifiedTime这个属性完全跟balance属性的修改操作绑定在一起 . 只有在balance修改的时候 , 它才会被修改 . 所以 , balanceLastModifiedTime的修改操作完全封装在了increaseBalance\(\)和decreaseBalance\(\)方法中 , 不对外暴露修改这个属性的方法和业务细节 , 也保证了balance和balanceLastModifiedTime的一致性 .

封装特性需要语言本身的语法支持 , 也就是**访问权限控制** .

#### 总结

封装也叫做信息隐藏或者数据访问保护 . 类通过暴露有限的访问接口 , 授权外部仅能通过类提供的方式来访问内部信息或者数据 .

它需要编程语言提供权限访问控制语法来支持 , 例如Java中的private , protected , public关键字 .

封装特性存在的意义 , 一方面是保护数据不被随意修改 , 提高代码的可维护性 ; 另一方面是仅暴露有限的必要接口 , 提高类的易用性 .

#### 抽象\(Abstraction\)

封装主要讲的是如何隐藏信息 , 保护数据 , 而抽象讲的是如何隐藏方法的具体实现 , 让调用者只需要关心方法提供了哪些功能 , 并不需要知道这些功能是如何实现的 . 在面向对象编程中 , 常借助编程语言提供的接口类\(interface\)或者抽象类\(abstract\)这两种语法机制 , 来实现抽象这一特性 .

```java
public interface IPictureStorage {
    void savePicture(Picture picture);
    Image getPicture(String pictureId);
    void deletePicture(String pictureId);
    void modifyMetaInfo(String pictureId, PictureMetaInfo metaInfo);
}

public class PictureStorage implements IPictureStorage {
    @Override
    public void savePicture(Picture picture) { ... }
    @Override
    public Image getPicture(String pictureId) { ... }
    @Override
    public void deletePicture(String pictureId) { ... }
    @Override
    public void modifyMetaInfo(String pictureId, PictureMetaInfo metaInfo) { ... }
}
```

上面的代码中 , 利用Java中的interface接口语法来实现抽象特性 . 调用者在使用图片存储功能的时候 , 只需要了解IPictureStorage这个接口类暴露了哪些方法就可以了 , 不需要知道具体的实现逻辑 .

抽象这个特性是非常容易实现的 , 并不需要非得依靠接口类或者抽象类这写特殊的语法机制 . 因为类的方法就是通过编程语言中的函数机制实现的 . 通过函数包裹具体的实现逻辑 , 这本身就是一种抽象 .

抽象是一个非常通用的设计思想 , 并不需要特殊的语法机制 , 只需要提供函数这一非常基础的语法机制 , 就可以实现抽象特性 , 所以有时候不被看做是面向对象编程的特性 .

很多设计原则都体现了抽象这种设计思想 , 比如基于接口而非实现编程 , 开闭原则 , 代码解耦等 . 一个简单的例子 , 定义类的方法时 , 也有抽象思想 , getAliyunPictureUrl\(\)命名为getPictureUrl\(\) , 如果以后存储的图片不在Aliyun上了 , 这个方法的命名也不需要修改 . 

#### 总结

封装主要讲如何隐藏信息 , 保护数据 , 那抽象就是讲如何隐藏方法的具体实现 , 让使用者只需要关心方法提供了哪些功能 , 不需要知道这些功能是如何实现的 . 抽象可以通过接口类或者抽象类来实现 , 但也并不需要特殊的语法机制来支持 . 抽象存在的意义 , 一方面是提高代码的可扩展性 , 维护性 , 修改实现不需要改变定义 , 减少代码的改动范围 ; 另一方面 , 它也是处理复杂系统的有效手段 , 能有效的过滤掉不必要关注的信息 .

