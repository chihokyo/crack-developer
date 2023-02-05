# Decorator 装饰器模式

这个模式也叫 Wrapper 模式。后面会讲为什么也叫这个。这个装饰器模式的核心就是什么呢？

> 在不改变源代码的逻辑情况下新增方法。

装饰器是一个很好的继承的替代方式，可以拓展很多方法。

## 出场角色

- **部件** （Component）→ 大多为抽象的
- **具体部件**（Concrete Component）→ 大多是具体的，会继承上面的抽象的。定义了一些基础行为，但是装饰器类会修改这些行为
- **抽象基础装饰** （Base Decorator）Decorator 一般是一个抽象类，实现接口或者抽象方法，其内部不一定有抽象方法定义，有可能只是单纯继承下 Component 抽象构件；但是其内部一般都有一个 Component 角色的引用，表示 Decorator 需要装饰的对象，一般该对象是 private 或者 protected 声明
- **具体装饰器类**（Concrete Decorator ）其实就是实现了上面抽象基础装饰的接口。

> 下面有几张图 我感觉还是会很好的阐明一下关系的。

![image-20230131172718060](https://raw.githubusercontent.com/chihokyo/image_host/develop/image-20230131172718060.png)

![img](http://images.gitbook.cn/67662860-71f3-11e8-be4d-f5aac07d9486)

这里先看一下最重要的装饰器的类

```java
package com.design.pattern.decorator;

/**
 * 装饰类 Decorator
 * 用于装饰其他类
 */
public class Decorator extends Drink {

    private Drink drink;

    /**
     * 组合的改变 新建一个装饰，那就必须传入一个实例对象（主饮品）
     * 比如珍珠奶茶即使加珍珠，也需要有个奶茶吧
     *
     * @param drink 饮品实例对象
     */
    public Decorator(Drink drink) {
        this.drink = drink;
    }

    @Override
    public float cost() {
        // 获取父类(奶茶)的价格+自己的价格（珍珠）
        return super.getPrice() + drink.cost();
    }

    @Override
    public String getDesc() {
        // super.getDesc() 装饰描述 加料
        // super.getPrice() 装饰价格 加料价格
        // drink.getDesc() 主要饮品的描述 这个其实是一个递归（本身也包裹了一层）
        return "配料: " + super.getDesc() + "价格: "
            + super.getPrice()+ ", === " + drink.getDesc();
    }
}

```

> 可以看出来，这里有可能是一层一层的层叠。都需要要求在父类的基础上添加一些方法。

## JS 实现

下面先看一下简单的基础实现

```js
// 基础咖啡
class Coffee {
  maker(water) {
    return `${water} + 咖啡`;
  }
  cost() {
    return 10;
  }
}

// 加奶的咖啡 （以前或许会用继承，但是这次用组合）
class MilkCoffee {
  constructor(parent) {
    this.parent = parent;
  }
  maker(water) {
    return `${this.parent.maker(water)} + 奶`;
  }
  cost() {
    return this.parent.cost() + 5;
  }
}

// 加糖的咖啡 （以前或许会用继承，但是这次用组合）
class SugarCoffee {
  constructor(parent) {
    this.parent = parent;
  }
  maker(water) {
    return `${this.parent.maker(water)} + 糖`;
  }
  cost() {
    return this.parent.cost() + 2;
  }
}

// 这个时候想要一个加奶咖啡怎么写？
// 把这个类似于父类的基础类穿进去
// 这样当你调用maker的时候，就是也会有父类的逻辑，在父类的基础上添加了一层逻辑
let milkcoffee = new MilkCoffee(new Coffee());
console.log(milkcoffee.maker('水'));

console.log(milkcoffee.cost());

// 这个时候如果我想要加奶加糖的咖啡就需要多层嵌套
let coffee = new Coffee();
let milkcoffee2 = new MilkCoffee(coffee);
let sugerMilkCoffee = new SugarCoffee(milkcoffee2);
console.log(sugerMilkCoffee.maker('水'));

console.log(sugerMilkCoffee.cost());
```

通过一层一层的实现，主要是基于上一层。

> 上面不是说为什么也叫 Wrapper 嘛？这是因为装饰器模式是讲一个对象嵌入到另一个对象之中，相当于包装起来。形成一条包装连。
>
> 请求就随着这条链然后传递到所有的对象。
>
> **有一种累加的效果。**

## AOP 概念

这个面向切面编程。主要体现在 Java 的 spring 里面。也是装饰器的一个体现。ES6 里面其实也有。这个以后写。那么 ES5 具体是怎么体现的呢？

```js
// 这里开始用ES5的模式模仿一段AOP
function bug(money, goods) {
  console.log(`我花了${money}块买了${goods}`);
}

Function.prototype.before = function (beforeFn) {
  // 此时的this就是bug 因为谁调用的谁就是this
  let _this = this;
  return function () {
    // 先执行你传入的函数
    beforeFn.apply(this, arguments);
    // 再执行_this也就是bug
    _this.apply(this, arguments);
  };
};

Function.prototype.after = function (afterFn) {
  // 此时的this就是bug
  let _this = this;
  return function () {
    _this.apply(this, arguments);
    // 要记住这里的顺序问题
    afterFn.apply(this, arguments);
  };
};

bug = bug.before(function () {
  console.log('🎉花钱之前我是土豪...');
});

bug = bug.after(function () {
  console.log('😭花钱之后我是穷逼...');
});

bug('1000', 'PS5');
```

这里有一段解释。

![image-20230131190148015](https://raw.githubusercontent.com/chihokyo/image_host/develop/image-20230131190148015.png)

这就相当于在不会改变 before 逻辑的前提下，对 buy

**这里犯了一个很弱智的错误，我把 buy 写成了 bug。**

应用场景

埋点也是可以的

## TS 实现

这里是直接参考的[TypeScript **装饰**模式讲解和代码示例](https://refactoringguru.cn/design-patterns/decorator/typescript/example)

```ts
// 角色1
interface Component {
  operation(): string;
}

// 角色2
class ConcreteComponent implements Component {
  public operation(): string {
    return 'ConcreteComponent';
  }
}

// 角色3
class Decorator implements Component {
  protected component: Component;

  constructor(componet: Component) {
    this.component = componet;
  }

  public operation(): string {
    return this.component.operation();
  }
}

// 角色4
class ConcreteDecoratorA extends Decorator {
  public operation(): string {
    return `ConcreteDecoratorA(${super.operation()})`;
  }
}

// 角色4
class ConcreteDecoratorB extends Decorator {
  public operation(): string {
    return `ConcreteDecoratorB(${super.operation()})`;
  }
}

// 客户端代码 传入一个具体组件
function clientCode(component: Component) {
  console.log(`RESULT: ${component.operation()}`);
}

// 这里是简单的 没被装饰的
const simple = new ConcreteComponent();
// console.log("Client: I've got a simple component:");
// clientCode(simple);

// 这里已经被装饰了（包裹了2层）
const decorator1 = new ConcreteDecoratorA(simple);
const decorator2 = new ConcreteDecoratorB(decorator1);
clientCode(decorator2);
```

## JS 和 TS 中的装饰器

这个待定...
