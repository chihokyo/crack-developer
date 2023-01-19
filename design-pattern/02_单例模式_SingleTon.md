# Singleton 单例模式

## 1. 概念导入

这个单例其实特别好理解，就是一个类（class），只有唯一的**实例。** 写代码的重点也是就这样。

```js
// 相当于无论你new了几次，都是同一个实例的感觉
const a = new Demo();
const b = new Demo();
console.log(a === b); // true

// 还有一种是不允许你new，但是使用人家类暴露的方法 但也是只有一个对象
const a = Demo.getInstance();
const b = Demo.getInstance();
console.log(a === b); // true
```

- 一个类只有一个实例
- 内部实例化（自己实例自己）
- 提供外部获得这个单一实例的方法

> 差不多接下来写代码也是沿用这样的思路。

其实写代码的时候最核心的逻辑不过就是一个

```typescript
if (!没有这个实例) {
  新建实例;
}
返回已有的实例;
```

## 2. Java 实现（没写）

Java 有七八种写法。线程安全+懒饿汉的排列组合有 n 种写法。这里不写这么多。抛砖引玉。

### 懒汉式（被动

懒汉就是懒，自己并不会主动创建。啥时候调用了，啥时候再创建。

```java

```

### 饿汉式（主动

类加载就给创建了，主动的。

```java

```

## 3. JS/TS 实现

**TS**是这样实现的，基本思路还是沿用了 Java 的

```typescript
class SingleTon {
  // 1 新建一个实例类 最后返回的是 new SingleTon
  private static instance: SingleTon;
  // 2 私有化 不准外部new
  private constructor() {}
  // 3 向外暴露的方法
  public static getInstance() {
    if (!SingleTon.instance) {
      SingleTon.instance = new SingleTon();
    }
    return SingleTon.instance;
  }
}
let s1 = SingleTon.getInstance();
let s2 = SingleTon.getInstance();
console.log(s1 === s2);
```

下面是**JS**的实现，可能比较困难。尤其跟 ES5+闭包联合起来。

```js
// ES6实现
class Singleton {
  // 向外暴露静态方法获取
  static getInstance() {
    // 判断是否有
    if (!Singleton.instance) {
      // 没有的话直接新建
      Singleton.instance = new Singleton();
    }
    return Singleton.instance;
  }
}

const s1 = Singleton.getInstance();
const s2 = Singleton.getInstance();
console.log(s1 === s2);

//实体类
function SingletonClose() {}

// ES5+闭包
SingletonClose.getInstance = (function () {
  // 1 新建1个内部变量 这个要跟上面的区分开
  // 上面SingletonClose.getInstance的是属于getInstance这个类的变量，这个变量是一个我们返回的函数
  // 而你这个是闭包内的变量
  let instance;
  return function () {
    // 2 判断是否有
    if (!instance) {
      // 3 在这里新建
      instance = new SingletonClose();
    }
    // 4 也就是说最后这个内部的instance其实是一个实例对象
    // 返回给了 SingletonClose.instance
    // 类似于这种感觉 SingletonClose.getInstance = 函数();
    // 类似于这种感觉 SingletonClose.getInstance = 上面的函数执行完 返回的是一个 new SingletonClose() ;
    return instance;
  };
})();

const s1 = SingletonClose.getInstance();
const s2 = SingletonClose.getInstance();
console.log(s1 === s2); // true
```

下面这一段是看一个教学视频，给的一段 JS 实现的清晰思路。

上面的 ES5+闭包采用的是类上的静态方法`getInstance()`来获取的单例。如果我想用的是 new 的方式呢？

```js
// ES5+闭包+new的方式
let Singleton = (function () {
  let singleton;
  return function (name) {
    if (singleton) {
      return singleton;
    } else {
      this.name = name;
      return (singleton = this);
    }
  };
})();

const s1 = new Singleton('chi');
const s2 = new Singleton('chin');
console.log(s1 == s2); //true
console.log(`${s1.name} and ${s2.name}`); // chi and chi
```

![image-20230118152428138](https://raw.githubusercontent.com/chihokyo/image_host/develop/image-20230118152428138.png)

> 上面的函数违反了单一职责原则。因为处理变量的逻辑，和 new 实例。都写在了一起。
>
> 下面写个拆分版本

个别类拆分版本

```js
// 实体类
function Phone(name) {
  this.name = name;
}

const createSingle = (function () {
  let instance;
  return function (name) {
    if (!instance) {
      instance = new Phone(name);
    }
    return instance;
  };
})();

const p1 = new createSingle('iphone');
const p2 = new createSingle('android');
console.log(p1 === p2); // true
console.log(`${p1.name} and ${p2.name}`); // iphone and iphone
```

> 🤔 由于上面的 createSingle 方法其实根本没用到 this，不 new，直接调用也是可以的

```js
const p3 = new createSingle('iphone');
const p4 = new createSingle('android');
console.log(p3 === p4); // true
console.log(`${p3.name} and ${p4.name}`); // iphone and iphone
```

上面的代码只能 new Phone，不具备通用性。这里写一个通用的拆分版本

```js
// 这里直接就是一个函数，返回一个函数
const createSingleTon = function (Constructor) {
  let instance;
  return function (...rest) {
    if (!instance) {
      instance = new Constructor(...rest);
    }
    return instance;
  };
};
// 实体类
function Dog(name) {
  this.name = name;
}
function Phone(price) {
  this.price = price;
}

// 这里返回的是一个函数
const DogSingle = createSingleTon(Dog);
const d1 = new DogSingle('wang');
const d2 = new DogSingle('cai');
console.log(d1.name); // wang
console.log(d2.name); // wang
console.log(d1 === d2); // true

const PhoneSingle = createSingleTon(Phone);
const p1 = new PhoneSingle(888);
const p2 = new PhoneSingle(777);
console.log(p1.price); // 888
console.log(p2.price); // 888
console.log(p1 === p2); // true
```

![image-20230118155538885](https://raw.githubusercontent.com/chihokyo/image_host/develop/image-20230118155538885.png)

## 应用场景

- 购物车只有一个车
- redux 全局只有一个唯一的 store（为了数据共享）
- 缓存（比如磁盘读取用户信息，每次都要从硬盘太慢了。所以就放在内存里的缓存里
- LRU 缓存（一套 leetcode 题目

我感觉上面的场景都是一个意思，就是在写入数据的时候提前判断当前是否存在。

```js
const Modal = (function () {
  let modal = null;
  return function () {
    // 当不存在的时候你才新建
    if (!modal) {
      modal = document.createElement('div');
      modal.innerHTML = '我是一个全局唯一的Modal';
      modal.id = 'modal';
      modal.style.display = 'none';
      document.body.appendChild(modal);
    }
    // 有的话直接返回
    return modal;
  };
})();

// 调用方
// 点击打开弹窗
document.getElementById('open').addEventListener('click', () => {
  // 不点击不创建 避免占用
  const modal = new Modal();
  modal.style.display = 'block';
});
// 点击关闭弹窗
document.getElementById('close').addEventListener('click', () => {
  // 这里就可以保证都是同一个modal实力
  const modal = new Modal();
  modal.style.display = 'none';
});
```
