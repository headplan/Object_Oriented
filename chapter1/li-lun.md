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

对于balance属性 , 也就是钱包中的余额 . 只能增减 , 不存在重新设置 . 所以 , 这里只暴露了两个方法 , increaseBalance\(\)和decreaseBalance\(\) , 也没有暴露set方法 . 



#### 抽象\(Abstraction\)



