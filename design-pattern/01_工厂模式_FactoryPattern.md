# Factory 工厂模式

首先这个模式是**创建型**的。在写代码的时候什么时候会有创建型呢？无外乎就是创建新的对象。也就是新的实例。

> 我感觉本质就是用你写一个方法 or 函数，来替代你 new 的行为。

## 1. 分类

具体的分类上会有这样三种

- 简单工厂 Simple Factory
- 工厂方法 Factory Method
- 抽象工厂 Abstract Factory

下面我一句话总结一下得了。

> 简单工厂。最常用，其实就是你不用手动 new 了，想要啥直接使用**传参**形式，让**别人**给你 new。
>
> 工厂方法。不怎么用。是为了解决简单工厂的一些弊端，就是开闭原则，每次修改还要破坏源代码。每次想要生产啥产品，**必须新建这个产品的工厂才行**。
>
> 抽象工厂。很少用。这个主要一个工厂可以生产 n 个东西。
>
> **写完之后感觉还是不如画图呢**

|          | 优点              | 缺点                                  | 登场人物                                      |
| -------- | ----------------- | ------------------------------------- | --------------------------------------------- |
| 简单工厂 | 不用你自己 new 了 | 判断太多 高耦合                       | 具体类 + 造具体类的方法（多为静态方法）       |
| 工厂方法 | 不用你判断了      | 一个产品（类）一个工厂 无法应对多产品 | 具体类 + 抽象工厂                             |
| 抽象工厂 | 一个工厂多个产品  | 没啥太大缺点，写起来类+抽象类太多     | （抽象工厂 + 具体工厂） + （抽象类 + 具体类） |

## 2. 没有任何套路的年代

这个是最简单的，先说以前我们创建类的时候常用的套路

```typescript
// 啥套路都没有的时候

/**
 * 通用手机父类
 */
class Phone {
  name: string;
  price: number;
  constructor(name: string, price: number) {
    this.name = name;
    this.price = price;
  }
  charge() {
    console.log(`${this.name} is charging now`);
  }
}

// IPhone子类
class IPhone extends Phone {
  os: string;
  constructor(name: string, price: number, os: string) {
    super(name, price);
    this.os = os;
  }
}
// Android子类
class Android extends Phone {
  os: string;
  constructor(name: string, price: number, os: string) {
    super(name, price);
    this.os = os;
  }
}

// 调用方
// 这个时候我想要new一个iPhone，就自己只能自己new
const iphone = new IPhone('iphone', 8888, 'ios');
const android = new Android('android', 5555, 'android');

iphone.charge();
android.charge();
```

> 上面的缺点
>
> - 你要自己 new 一下，new 一个 iPhone 还可以，new100 个 iPhone 呢。每次都要写。你还要知道自己怎么才能 new。
> - 你要自己亲自引入 iPhone 这个类。这就是耦合。

## 3. 简单工厂（Simple Factory）

这个时候简单工厂就来了，我再也不想自己 new 了。别人给我 new 就行了。

```typescript
class Factory {
  static createPhone(type: string) {
    switch (type) {
      case 'iphone':
        return new IPhone('iphone', 8888, 'ios');
      case 'android':
        return new Android('android', 5555, 'android');
      default:
        throw new Error('这个真没有');
    }
  }
}

// 调用方
const iphone2 = Factory.createPhone('iphone');
const android2 = Factory.createPhone('android');
iphone2.charge();
android2.charge();
```

上面可以看出来，还需要你 new 吗？不需要了。你只要干什么？`const iphone2 = Factory.createPhone('iphone');` 传个参数就行

即使有一天，你不想叫 IPhone 了。想要别的，你只要修改`Factory.createPhone()`这个方法就可以。你自然传参。

即使有一天，你想增加一个小米的手机。你直接可以增加一个`class Xiaomi{}` 然后增加一个 case 就可以

```typescript
// 新增 XiaoMi子类
class XiaoMi extends Phone {
  os: string;
  constructor(name: string, price: number, os: string) {
    super(name, price);
    this.os = os;
  }
}

class Factory {
  static createPhone(type: string) {
    switch (type) {
      case 'iphone':
        return new IPhone('iphone', 8888, 'ios');
      case 'android':
        return new Android('android', 5555, 'android');
      // 新增一个case就可以
      case 'xiaomi':
        return new XiaoMi('xiaomi', 5555, 'mios');
      default:
        throw new Error('这个真没有');
    }
  }
}

const xiaomi = Factory.createPhone('xiaomi');
xiaomi.charge();
```

上面方法的缺点

> 违背了开闭原则。对扩展开放，对修改封闭。你新增一个 Xiaomi，还要修改破坏掉`Factory.createPhone()`这个方法。
>
> siwtchcase 有可能过于冗长。如果有 100 个产品，要写 100 个 case 才行。

### 使用场景

- jQuery 的实现（选择器）
- 虚拟 DOM

```typescript
/**
 * 使用场景 jQuery
 */
class Jquery {
  length: number;
  constructor(selector: string) {
    let elements = Array.from(document.querySelectorAll(selector));
    let length = elements ? elements.length : 0;
    for (let i = 0; i < length; i++) {
      this[i] = elements[i];
    }
    this.length = length;
  }
  html() {}
}

window.$ = function (selector) {
  return new Jquery(selector);
};

/**
 * 使用场景 虚拟DOM
 */
class VNode {
  tagName: string;
  attr: string;
  children: string;
  constructor(tagName, attr, children) {
    this.tagName = tagName;
    this.attr = attr;
    this.children = children;
  }
}

function createElement(tagName, attr, children) {
  return new VNode(tagName, attr, children);
}
```

## 4. 工厂方法（Factory Method）

为了解决上面的问题，出来了工厂方法。每一次我们新 new 一个，就要新建一个工厂。而新建的这个工厂有共通的父类大工厂。

首先实体类和上面的是一模一样的。

```typescript
/**
 * 实体类们
 */
class Phone {
  name: string;
  price: number;
  constructor(name: string, price: number) {
    this.name = name;
    this.price = price;
  }
  charge() {
    console.log(`${this.name} is charging now`);
  }
}

// IPhone子类
class IPhone extends Phone {
  os: string;
  constructor(name: string, price: number, os: string) {
    super(name, price);
    this.os = os;
  }
}
// Android子类
class Android extends Phone {
  os: string;
  constructor(name: string, price: number, os: string) {
    super(name, price);
    this.os = os;
  }
}
```

最重要的是下面的工厂类们

```typescript
/**
 * 超级大工厂
 */
class SuperFactory {
  create() {}
}

// 每一个工厂都要实现父类的方法
class IPhoneFactory extends SuperFactory {
  static create(name: string, price: number, os: string) {
    console.log('我只制造 iPhone');
    return new IPhone(name, price, os);
  }
}

// 每一个工厂都要实现父类的方法
class AndriodFactory extends SuperFactory {
  static create(name: string, price: number, os: string) {
    console.log('我只制造 Android');
    return new Android(name, price, os);
  }
}

// 调用的时候
//调用方
const iphone = IPhoneFactory.create('iphon14', 8888, 'ios');
iphone.charge();

const andriod = IPhoneFactory.create('android', 2222, 'cookie');
andriod.charge();
```

工厂一般是一个接口。生产什么产品，就实现这个工厂的接口自己搞一个工厂。

平常我们写代码的时候不是都有这种情况嘛

```js
const Demo = require(...);// 引入一个
// const d = new Demo() 有时候我们并不是这样来创建一个实例的
const d = Demo.create("你想传入的参数"); // 这个create本质不就是一个工厂么
```

这样的话。我们以后如果增加一个小米工厂。直接增加一个小米实体类+小米工厂。可以了。

> 上面的方法有什么缺陷呢？
>
> 每一个产品就需要一个工厂，实在太浪费资源了。如果产品特别多的情况，或者说 IPhone 工厂下可能还有 iPhone11,12,13 三个工厂。那怎么办呢？于是抽象工厂就出来了。

## 5. 抽象工厂 abstract factory

上面的工厂方法，不是一个厂子只能生产一个产品吗。这个抽象工厂就不是的了，而是一个厂子生产多个产品

实体类如下。

```typescript
/**
 * 实体类们
 */
// （完全可以用抽象方法or接口）
class Phone {
  name: string;
  price: number;
  constructor(name: string, price: number) {
    this.name = name;
    this.price = price;
  }
  charge() {
    console.log(`${this.name} is charging now`);
  }
}

// IPhone子类
class IPhone extends Phone {
  os: string;
  constructor(name: string, price: number, os: string) {
    super(name, price);
    this.os = os;
  }
}
// Android子类
class Android extends Phone {
  os: string;
  constructor(name: string, price: number, os: string) {
    super(name, price);
    this.os = os;
  }
}

// 耳机父类（完全可以用抽象方法or接口）
class Earphone {
  name: string;
  price: number;
  constructor(name: string, price: number) {
    this.name = name;
    this.price = price;
  }
}

// AirPods 子类
class AirPods extends Earphone {
  constructor(name: string, price: number) {
    super(name, price);
  }
  listen() {
    console.log(`${this.name} is listening now`);
  }
}
// Ears子类
class Ears extends Earphone {
  constructor(name: string, price: number) {
    super(name, price);
  }
  watching() {
    console.log(`${this.name} is watching now`);
  }
}
```

具体的工厂

```typescript
/**
 * 一个超级大的抽象工厂
 */
interface BigFactory {
  // 制造手机
  createPhone(name: string, price: number, os: string): Phone;
  // 制造耳机
  createEar(name: string, price: number): Earphone;
}

class AppleFactory implements BigFactory {
  createPhone(name: string, price: number, os: string): Phone {
    return new IPhone(name, price, os);
  }
  createEar(name: string, price: number): Ears {
    return new Ears(name, price);
  }
}
```

最后的调用者

```typescript
// 调用开始
// 比如此时我想生产一套苹果全家桶设备
const apple = new AppleFactory();
const iphone = apple.createPhone('iphone13', 7777, 'ios8');
const airpods = apple.createEar('airpods3', 233);
iphone.charge();
airpods.watching();
```

> 这样就实现了一家工厂可以制造多个设备的想法。
>
> 当有一天，我们不仅仅想生产苹果，有可能是安卓的设备那么你就新增一个工厂 + 你需要创建的实体类就可以了。

```typescript
// 只要自己新建一个工厂 新建实体类 就可以自由生产
class AndriodFactory implements BigFactory {
  createPhone(name: string, price: number, os: string): Phone {
    return new Android(name, price, os);
  }
  createEar(name: string, price: number): Ears {
    return new Ears(name, price);
  }
}

const andriod = new AndriodFactory();
const galaxy = andriod.createPhone('andriod', 2222, 'ios8');
const sony = andriod.createEar('sony', 933);
galaxy.charge();
sony.watching();
```
