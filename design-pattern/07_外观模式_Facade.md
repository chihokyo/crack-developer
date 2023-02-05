# Facade 外观模式

这个模式不是很难。理解起来不是很难，代码写起来也不是很难。

> 模式就是把一些复杂的流程封装成一个接口供给外部用户更简单的使用

个人总结一下，就是提供一个统一的接口，你只要调用这个统一的接口。那么这个接口会把复杂的东西都给你结合起来。比如你有一个接口，可能要用到十几个类。难道你要 1 个个调用吗？不如写一个门面让她帮你了。

这个就是我平常写代码的时候。比如导出包，可以有一个统一的接口。然后你统一导出。

## 主要角色

- **外观** （Facade） 其实就是门面，你就调用这个
- **复杂子系统** （Complex Subsystem） 这个其实就是外观里面的，你可以在门面里面调用这些复杂的子系统
- 客户端 → 不多说了 就是简单的调用

## JS 实现

```js
// 复杂的子系统
class CPU {
  start() {
    console.log('打开CPU');
  }
}
class Memory {
  start() {
    console.log('打开内存');
  }
}
class Disk {
  start() {
    console.log('打开硬盘');
  }
}

// Facade门面
class Computer {
  constructor() {
    this.cpu = new CPU();
    this.memory = new Memory();
    this.disk = new Disk();
  }
  // 写一个接口 让下面的都开始 下面的逻辑都是你写的
  allStart() {
    this.cpu.start();
    this.memory.start();
    this.disk.start();
  }
}

// 客户端调用
const computer = new Computer();
// 这里你无需自己亲自new，然后所有的动作都会按照你的逻辑来运行
// 可以说就是通过一个简单的接口，实现复杂的逻辑调用
computer.allStart();
```

## TS 实现

其实逻辑和上面 JS 几乎一样的。

```typescript
// 复杂子系统
class Subsystem1 {
  public operation1(): string {
    return '子系统1操作1 Subsystem1: Ready!\n';
  }

  // 这里你可以写n个

  public operationN(): string {
    return '子系统1操作N SubsystemN: Go!\n';
  }
}

class Subsystem2 {
  public operation1(): string {
    return '子系统2操作1 Subsystem1: Ready!\n';
  }

  // ...

  public operationZ(): string {
    return '子系统2操作Z SubsystemN: Go!\n';
  }
}

//门面
class Facade {
  protected subsystem1: Subsystem1;
  protected subsystem2: Subsystem2;

  // 你可以选择已有的，也可以新建
  constructor(subsystem1?: Subsystem1, subsystem2?: Subsystem2) {
    this.subsystem1 = subsystem1 || new Subsystem1();
    this.subsystem2 = subsystem2 || new Subsystem2();
  }

  public operation(): string {
    let result = '下面是一段复杂的逻辑的开始..Facade initializes 初始化:\n';
    result += this.subsystem1.operation1();
    result += this.subsystem2.operation1();
    result +=
      '下面是一段复杂的逻辑的最后一步..Facade orders subsystems to perform the action:\n';
    result += this.subsystem1.operationN();
    result += this.subsystem2.operationZ();

    return result;
  }
}

// 客户端代码
function client(facade: Facade) {
  console.log(facade.operation());
}

const s1 = new Subsystem1();
const s2 = new Subsystem2();
const facade = new Facade(s1, s2);
client(facade);
```

## 实际使用场景

比如下面这一段 redux，所有的出口文件。就我们导入第三方包的时候不是经常用`import {xxx} from "redux"`的吗？其实就是有了一个统一的出口。复杂的逻辑都交给内部了。

[redux src/index.ts](https://github.com/reduxjs/redux/blob/master/src/index.ts)

```typescript
// functions
import { createStore, legacy_createStore } from './createStore';
import combineReducers from './combineReducers';
import bindActionCreators from './bindActionCreators';
import applyMiddleware from './applyMiddleware';
import compose from './compose';
import __DO_NOT_USE__ActionTypes from './utils/actionTypes';

// types
// store
export {
  CombinedState,
  PreloadedState,
  Dispatch,
  Unsubscribe,
  Observable,
  Observer,
  Store,
  StoreCreator,
  StoreEnhancer,
  StoreEnhancerStoreCreator,
  ExtendState,
} from './types/store';
// reducers
export {
  Reducer,
  ReducerFromReducersMapObject,
  ReducersMapObject,
  StateFromReducersMapObject,
  ActionFromReducer,
  ActionFromReducersMapObject,
} from './types/reducers';
// action creators
export { ActionCreator, ActionCreatorsMapObject } from './types/actions';
// middleware
export { MiddlewareAPI, Middleware } from './types/middleware';
// actions
export { Action, AnyAction } from './types/actions';

export {
  createStore,
  legacy_createStore,
  combineReducers,
  bindActionCreators,
  applyMiddleware,
  compose,
  __DO_NOT_USE__ActionTypes,
};
```

下面也是一个我在实际写代码的时候经常用到的。

![image-20230205014328394](https://raw.githubusercontent.com/chihokyo/image_host/develop/image-20230205014328394.png)
