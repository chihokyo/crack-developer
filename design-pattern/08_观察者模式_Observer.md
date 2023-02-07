# Observer 观察者模式

我感觉这个模式，就是订阅的感觉。你不用每天去 check，只要订阅了，人家就会给你发东西过来。

其实 JS 的各种事件，比如点击事件本质上不就是一种观察吗？监听到了你点击，然后执行。其实每次@别人就是一种观察者模式。

## 出场角色

- 观察者（抽象）
- 观察者（具体）
- 被观察者（抽象）
- 被观察者（具体）

我感觉抽象都是用来制约具体的。抽象写一些方法，具体再来实现

> 被观察者和观察者是耦合的。（被观察者里面包含着观察者，维护一个数据而已
>
> 观察者的 update 也是**被观察者**调用的。

## JS 实现

```js
// 被观察者
class Superstar {
  constructor(name) {
    this.name = name;
    this.state = '';
    this.observers = [];
  }
  getState() {
    return this.state;
  }

  setState() {
    this.state = this.state;
    this.notifyAllObservers();
  }

  // 增加新的观察者
  attach(observer) {
    this.observers.push(observer);
  }
  notifyAllObservers() {
    if (this.observers.length > 0) {
      this.observers.forEach((o) => o.update());
    }
  }
}

// 粉丝 观察者
class Fan {
  constructor(name, superstar) {
    this.name = name;
    this.superstar = superstar;
    // 这里很重要 相当于把自己的这个实例放进去上面的
    this.superstar.attach(this);
  }
  update() {
    console.log(
      `${this.superstar.name}有新的状态-${this.superstar.getState()},${
        this.name
      }正在更新`
    );
  }
}
const superstar = new Superstar('CHIN');
// 相当于新增一个粉丝
const fan1 = new Fan('dog', superstar);
const fan2 = new Fan('cat', superstar);
// 一旦状态改变 你会发现都更新了
superstar.setState('结婚了');
```

## TS 实现

下面我是实现了抽象的，完成的是通知粉丝，我的粉丝数。当大于 2 的时候通知猫，少于 3 的时候通知够。

```js
export {};
// 被观察者（抽象）
interface Star {
  attach(observer: Observer): void;
  detach(observer: Observer): void;
  notify(): void;
}
// 观察者（抽象）
interface Observer {
  update(star: Star): void;
}

// 被观察者（具体）

class SuperStar implements Star {
  public state: number = 0;
  private observers: Observer[] = [];

  public attach(observer: Observer): void {
    const isExist = this.observers.includes(observer);
    if (isExist) {
      return console.log('存在这个观察者');
    }
    this.observers.push(observer);
  }
  public detach(observer: Observer): void {
    // 看一下是否存在
    const observerIndex = this.observers.indexOf(observer);
    if (observerIndex == -1) {
      console.log('不存在这个观察者 无法删除');
    }
    // 删除这个观察者
    this.observers.splice(observerIndex, 1);
  }

  // 通知其他类
  public notify(): void {
    console.log('开始通知粉丝啦');
    this.observers.forEach((obsever) => obsever.update(this));
  }

  // 一些其他逻辑
  public someBusinessLogic(): void {
    // 初始化一个数字表示state
    console.log('开始初始化');
    this.state = Math.floor(Math.random() * (10 + 1));
    console.log(`现在共有${this.state}粉丝`);
    this.notify();
  }
}

class Dog implements Observer {
  public update(star: Star): void {
    // 判断一下传入的是否
    if (star instanceof SuperStar && star.state < 3) {
      console.log('粉丝数少于3');
    }
  }
}
class Cat implements Observer {
  update(star: Star): void {
    if (star instanceof SuperStar && (star.state === 0 || star.state >= 2)) {
      console.log('粉丝数在2个以上');
    }
  }
}

const superstar = new SuperStar();
const fan1 = new Dog();
superstar.attach(fan1);
const fan2 = new Cat();
superstar.attach(fan2);
superstar.someBusinessLogic();
superstar.someBusinessLogic();

superstar.detach(fan2);

```

## 应用场景

- JS 的事件本质就是一个观察者模式

```js
const dom = document.querySelector('#id');
// 下面不就是观察吗。函数只有在你click之后，也就是监听到了才执行。
dom.addEventListener('click', function () {
  console.log('clicked');
});
```

- Promise 也是一个

内部原理其实就是一个监听。下面这一段代码实现了一个逻辑，就是经过 1 秒后打印函数。

```js
class Promise {
  constructor(fn) {
    this.callbacks = [];
    let resolve = () => {
      this.callbacks.forEach((callback) => callback());
    };
    fn(resolve);
  }
  then(callback) {
    this.callbacks.push(callback);
  }
}
let promise = new Promise(function (resolve) {
  setTimeout(function () {
    // 延迟1s之后执行resolve方法。
    resolve(100);
  }, 1000);
});
// then相当于注册监听函数
promise.then(() => console.log(1));
promise.then(() => console.log(2));
```

这个我看了几次才明白。then 相当于注册函数（观察者），resolve 相当于被观察者。一旦 resolve，就开始执行 then 里面缓存的函数。

![image-20230207174908318](https://raw.githubusercontent.com/chihokyo/image_host/develop/image-20230207174908318.png)

- jQuery.Callbacks

- JQuery.Callbacks 是 jQuery1.7+之后引入的，用来进行函数队列的 add、remove、fire、lock 等操作
- 并提供 once、memory、unique、stopOnFalse 四个 option 进行一些特殊的控制。
- Callbacks 对象其实就是一个函数队列，获得 Callbacks 对象之后 ，就可以向这个集合中增加或者删除函数。add 和 remove 功能相反，函数参数是相同的，empty()删除回调列表中的所有函数

这里手写一个。

```js
function CallBacks() {
  let observers = [];
  function add(fn) {
    observers.push(fn);
  }
  function remove(fn) {
    let index = observers.indexOf(fn);
    if (index != -1) {
      observers.splice(index, 1);
    }
  }
  function fire() {
    observers.forEach((f) => f());
  }
  return {
    add,
    remove,
    fire,
  };
}

let callbacks = CallBacks();

let f1 = () => console.log(1);
let f2 = () => console.log(2);
let f3 = () => console.log(3);

callbacks.add(f1);
callbacks.add(f2);
callbacks.add(f3);
callbacks.remove(f2);
// 这就类似一个通知，fire之后全部都开始执行。维护了一个函数队列。
callbacks.fire();
```

- Node.js EventEmitter 这是一个 Node 事件

因为没有 Node 环境，所以先暂时写一个。

```js
var EventEmitter = require('events').EventEmitter;
var event = new EventEmitter();
// 相当于注册了一个事件
event.on('some_event', function () {
  console.log('some_event 事件触发');
});
setTimeout(function () {
  // 1秒后发送这个时间，就是观察者了
  event.emit('some_event');
}, 1000);
```

> 运行这段代码，1 秒后控制台输出了 **'some_event 事件触发'**。其原理是 event 对象注册了事件 some_event 的一个监听器，然后我们通过 setTimeout 在 1000 毫秒以后向 event 对象发送事件 some_event，此时会调用 some_event 的监听器。

下面自己手写一个

```js
class MyEventEmitter {
  constructor() {
    this._events = {};
  }

  on(type, listener) {
    // 第一次肯定都是undefined的
    let listeners = this._events[type];
    // 有的话就push
    if (listeners) {
      listeners.push(listener);
    } else {
      this._events[type] = [listener];
    }
  }
  emit(type) {
    // 为什么用数组呢？因为可以存储多个事件
    let listeners = this._events[type];
    // 获取所有参数
    let args = Array.from(arguments).slice(1);
    // 执行
    listeners.forEach((listener) => listener(...args));
  }
}

const event = new MyEventEmitter();
event.on('some_event', function () {
  console.log('some_event 事件触发');
});
event.on('one_more', function () {
  console.log('再来一个事件');
});
setTimeout(function () {
  event.emit('some_event');
  event.emit('one_more');
}, 1000);
```

- redux 的发布订阅

```js
function createStore(reducer) {
  let state;
  let listeners = [];
  function getState() {
    return state;
  }

  function subscribe(listener) {
    listeners.push(listener);
  }

  function dispatch(action) {
    // 相当于更新state
    state = reducer(state, action);
    // 这里就相当于通知所有的
    listeners.forEach((listener) => listener());
  }

  return {
    getState,
    subscribe,
    dispatch,
  };
}
```

## 观察者和发布订阅区别

这个经常有人混淆，其实区别很简单。

> **就是有没有中介**

可以看到发布订阅模式的时候，有一个**事件中心**。也就是第三方，由第三方去感知。就是发布订阅。

![-w316](http://wsk-mweb.oss-cn-hangzhou.aliyuncs.com/2020/01/27/15801108638995.jpg)

下面写一个有第三方参与的。

> - 虽然两种模式都存在订阅者和发布者（观察者可认为是订阅者、被观察者可认为是发布者）
> - 但是观察者模式是由被观察者调度的，而发布/订阅模式是统一由调度中心调的
> - 所以观察者模式的订阅者与发布者之间是存在依赖的，而发布/订阅模式则不会。

这里用 JS 实现了

```js
// 中介（第三方）
class Agency {
  constructor() {
    this._events = {};
  }
  subscribe(type, listener) {
    let listeners = this._events[type];
    if (listeners) {
      listeners.push(listener);
    } else {
      // 这里要放一个数组
      this._events[type] = [listener];
    }
  }
  publish(type) {
    let listeners = this._events[type];
    let args = Array.prototype.slice.call(arguments, 1);
    if (listeners) {
      listeners.forEach((listener) => listener(...args));
    }
  }
}

// 房东 （被观察者）
class LandLord {
  constructor(name) {
    this.name = name;
  }
  // 发布中介信息
  lend(agent, area, money) {
    agent.publish('house', area, money);
  }
}

// 房客（观察者）
class Client {
  constructor(name) {
    this.name = name;
  }
  rent(agent) {
    agent.subscribe('house', (area, money) => {
      console.log(`${this.name}已经看到中介的房子了 ${area} ${money}`);
    });
  }
}

const agent = new Agency();
const c1 = new Client('chin租房');
const c2 = new Client('tom租房');
const l1 = new LandLord('大地主');
c1.rent(agent);
c2.rent(agent);
l1.lend(agent, '30平方', '3000万');
```

最后的输出是

```
chin租房已经看到中介的房子了 30平方 3000万
tom租房已经看到中介的房子了 30平方 3000万
```

> 其实仔细看上面的代码，观察者（租房的）主要是通过中介耦合，然后中介耦合房东来完成的。租客完全是不用知道房东是谁的。房东也不知道租客是谁，只是通过耦合中介这个对象来完成的。
