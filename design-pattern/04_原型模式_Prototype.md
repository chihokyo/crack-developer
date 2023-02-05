# Prototype 原型模式

这个模式其实说起来蛮有意思的，就是 JS 其实就是很标准的 prototype。这个其实怎么说呢。JS 的出生就是自带这个原型。

当我们想创建**多个一样的对象**的时候，在 Java 这种严格的 OOP 模式里，是不是只能 new 一个对象呢？是的！但是不 new 就产生不了对象了吗？原理是上的，但我们可以使用**克隆**技术！完成对一个对象的复制！

原型模式不单是一种设计模式，也是一种编程范型。简单理解原型模式 Prototype：**不根据类来生成实例，而是根据实例生成新的实例**。也就说，如果需要一个和某对象一模一样的对象，那么就可以使用原型模式。

- JS 天然自带 Prototype 属性
- Java 可以用克隆

> 不需要你自己 new，需要你对象的基础上生成新对象。
>
> **传统** 类 → 生成新对象
>
> **原型模式** 对象 → 生成新对象

## Java

```java
// before 在没有克隆的时候如果你想新建多个一样的对象

/**
 * 这里写一个羊
 */
public class Sheep {
    private String name;
    private int age;
    private String color;

    public Sheep(String name, int age, String color) {
        super();
        this.name = name;
        this.age = age;
        this.color = color;
    }

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return this.age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getColor() {
        return this.color;
    }

    public void setColor(String color) {
        this.color = color;
    }

    @Override
    public String toString() {
        return "{" + " name='" + getName() + "'" + ", age='" + getAge() + "'" + ", color='" + getColor() + "'" + "}";
    }

}

// 调用方克隆起来

public class Client {
    public static void main(String[] args) {

        Sheep sheep = new Sheep("tom", 1, "white");

        Sheep sheep2 = new Sheep(sheep.getName(), sheep.getAge(), sheep.getColor());
        Sheep sheep3 = new Sheep(sheep.getName(), sheep.getAge(), sheep.getColor());
        Sheep sheep4 = new Sheep(sheep.getName(), sheep.getAge(), sheep.getColor());
        Sheep sheep5 = new Sheep(sheep.getName(), sheep.getAge(), sheep.getColor());
        // ...

        System.out.println(sheep); // { name='tom', age='1', color='white'}
        System.out.println(sheep2); // { name='tom', age='1', color='white'}
        System.out.println(sheep3); // { name='tom', age='1', color='white'}
        System.out.println(sheep4); // { name='tom', age='1', color='white'}
        System.out.println(sheep5); // { name='tom', age='1', color='white'}
    }
}
```

> 上面的方法实在是太鸡肋了。那怎么才能通过克隆的方式呢？Java 提供了一个**Cloneable**接口

```java
// after
public class Sheep implements Cloneable {
    private String name;
    private int age;
    private String color;

    public Sheep friend;

	 // ..... 和上面一样，先省略

    // 克隆该实例，使用默认的 clone 方法来完成
    @Override
    protected Object clone() {
        Sheep sheep = null;
        try {
          // 🔥 重点就在于这里 使用super.clone()然后强转
            sheep = (Sheep) super.clone();
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
        return sheep;
    }

}

```

## JS 自带原型模式

为什么这么说呢？因为当我们想创造一个对象的时候，在 JS 里完全可以使用**原型**来创建一个对象。

什么叫使用原型对象呢？意思就是说我不用 new 就可以创建对象，可以在现有的对象身上，创造一个新对象。

```js
// 原有的对象
const person = {
  isHuman: false,
  printIntroduction: function () {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  },
};

// 新对象
const me = Object.create(person);
me.name = 'CHIN'; // name这个属性是me有的，person这个对象没有
me.isHuman = true; // 重写继承过来的属性为true
me.printIntroduction();
```

Object.create()方法是 ECMAScript 5 中新增的方法，这个方法用于创建一个新对象，使用现有的对象来提供新创建的对象的**proto**。被创建的对象会继承另一个对象的原型，在创建新对象时还可以指定一些属性。

[这篇文章还是可以的 js 高级---Object.create()方法实现对象继承与创建新的 JavaScript 对象](https://blog.csdn.net/muzidigbig/article/details/89179459)
