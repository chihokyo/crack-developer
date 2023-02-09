# State 状态模式

> 状态模式 (State Pattern)允许一个对象在其内部状态改变时改变它的行为，对象看起来似乎修改了它的类，类 的行为随着它的状态改变而改变。

这个模式我刚开始没有理解清楚，感觉看起来很繁琐。

首先状态模式重点是状态。什么状态下能干什么事儿，这就是状态的重点。

还有下载文件的时候，就有好几个状态，比如下载验证、下载中、暂停下载、下载完毕、失败，文件在不同状态下 表现的行为也不一样，比如下载中时显示可以暂停下载和下载进度，下载失败时弹框提示并询问是否重新下载等 等。类似的场景还有很多，比如电灯的开关状态、电梯的运行状态等，女生作为你的朋友、好朋友、女朋友、老婆 等不同状态的时候，行为也不同 。

```
在这些场景中，有以下特点:
```

- 对象有有限多个状态，且状态间可以相互切换
- 各个状态和对象的行为逻辑有比较强的对应关系，即在不同状态时，对应的处理逻辑不一样

这就是状态模式下讨论的重点。

```js

```

> 状态模式的目的是为了把上述一大串`if...else...`的逻辑给分拆到不同的状态类中，使得将来增加状态比较容易。

## 主要角色

- Context 上下文 → 你可以简单的理解成**管理状态的逻辑的**
- State 抽象状态类 → 状态类，只不过是抽象的。
- ConcreteState 具体状态类 → 上面的实现

## 未使用

下面是一段没有使用状态模式的代码

```js
class Battery {
  constructor() {
    this.amount = 'high';
  }
  show() {
    if (this.amount == 'high') {
      console.log('绿色');
      this.amount = 'middle';
    } else if (this.amount == 'middle') {
      console.log('黄色');
      this.amount = 'low';
    } else {
      console.log('红色');
    }
  }
}
let battery = new Battery();
battery.show(); // 第一次调用之后绿色，然后状态成middle
battery.show(); // 第二次之后状态就是middle了
battery.show(); // 第三次就是low了
```

> 上面的问题就是
>
> - if else 很多
> - 逻辑和修改状态逻辑都写在了一起

那么使用状态模式之后呢？

```js
class Battery {
  constructor(state) {
    this.amount = 'high';
    this.state = new SuccessState();
  }
  show() {
    this.state.show();
    if (this.amount == 'high') {
      this.amount = 'middle';
      this.setState(new WarningState());
    } else if (this.amount == 'middle') {
      this.amount = 'low';
      this.setState(new DangerState());
    }
  }
  setState(state) {
    this.state = state;
  }
}
class SuccessState {
  constructor(battery) {
    this.battery = battery;
  }
  show() {
    console.log(`绿色 ${battery.amount}`);
  }
}
class WarningState {
  constructor(battery) {
    this.battery = battery;
  }
  show() {
    console.log(`黄色 ${battery.amount}`);
  }
}
class DangerState {
  constructor(battery) {
    this.battery = battery;
  }
  show() {
    console.log(`红色 ${battery.amount}`);
  }
}

let battery = new Battery();
battery.show();
battery.show();
battery.show();
```

可以发现一些不一样的地方

- 多了很多类，一个状态一个类
- 多了一个控制类的中枢（上下文）
- 但是并不能减少 if.else，因为不同于策略模式。改变状态逻辑依然要写在内部。

## 应用场景

一个通过状态控制点赞与否的

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>状态模式</title>
  </head>
  <body>
    <div id="root"></div>
    <script>
            // 以下就相当于具体状态类
            // 没有赞情况下 就是赞
            let unlikeState = {
              render(element) {
                element.innerHTML = '赞';
              },
            };

            // 在已经赞过的状态下 只能是取消
            let likedState = {
              render(element) {
                element.innerHTML = '取消';
              },
            };

            // 相当于上下文
            class Button {
              constructor(container) {
                this.liked = false; // 初始状态是没有赞
                this.state = unlikeState;
                // 给节点添加
                this.element = document.createElement('button');
                container.appendChild(this.element);
                this.render();
              }
              setState(state) {
                // 状态改变之后  要重新渲染自己
                this.state = state;
                this.render();
              }
              // 渲染节点
              render() {
                this.state.render(this.element);
              }
            }

            let button = new Button(document.body);
      // 看好了 这里一定要是element，直接写body不是dom元素      button.element.addEventListener('click', function () {
              // 已经赞的情况下 就是取消，否则就是赞 (这里修改的是行为的状态)
              button.setState(button.liked ? unlikeState : likedState);
              // 修改完之后这里也要改(这里修改的是内部的用来判定上面行为的)
              button.liked = !button.liked;
            });
    </script>
  </body>
</html>
```

React 的 state

这个就是一个典型的状态模式，通过 state 的改变同时改变。

```jsx
 <script type="">
      const States = {
        show: function () {
          console.log('banner显示，点击可以关闭');
          this.setState({
            currentState: 'hide',
          });
        },
        hide: function () {
          console.log('banner隐藏，点击可以打开');
          this.setState({
            currentState: 'show',
          });
        },
      };
      class Banner extends React.Component {
        state = { currentState: 'show' };
        toggle = () => {
          let s = this.state.currentState;
          States[s] && States[s].apply(this);
        };
        render() {
          return (
            <div>
              {this.state.currentState == 'show' && <nav>导航</nav>}
              <button onClick={this.toggle}>隐藏</button>
            </div>
          );
        }
      }
      ReactDOM.render(<Banner />, document.getElementById('root'));
    </script>
```

- 手写状态机

下面这段代码的思想还蛮重要的。主要是实现一个 JS 库的状态机。[_javascript-state-machine_](https://github.com/jakesgordon/javascript-state-machine)

```js
var fsm = new StateMachine({
  init: 'solid',
  transitions: [
    { name: 'melt', from: 'solid', to: 'liquid' },
    { name: 'freeze', from: 'liquid', to: 'solid' },
    { name: 'vaporize', from: 'liquid', to: 'gas' },
    { name: 'condense', from: 'gas', to: 'liquid' },
  ],
  methods: {
    onMelt: function () {
      console.log('I melted');
    },
    onFreeze: function () {
      console.log('I froze');
    },
    onVaporize: function () {
      console.log('I vaporized');
    },
    onCondense: function () {
      console.log('I condensed');
    },
  },
});
```

我感觉这个就是告诉你，目前是这么状态。可以从一个状态变成另一个状态使用了什么方法。

- form：当前行为从哪个状态来
- to:当前行为执行完会过渡到哪个状态
- name:当前行为的名字

```js
{ name: 'melt',     from: 'solid',  to: 'liquid' },
```

下面自己手写这个状态机

```js
// 下面是手写一个状态机
// javascript-state-machine原本是这个库的
class StateMachine {
  constructor(options) {
    // 先结构出全部options
    let { init, transitions, methods } = options;
    // 设置初始状态
    this.state = init;
    transitions.forEach((transition) => {
      let { name, from, to } = transition;
      // 这里相当于给自身添加一些方法了
      this[name] = function () {
        // 如果此时的状态是from
        if (this.state === from) {
          // 那么1 先设置状态成to
          this.state = to;
          // 然后拼接方法
          let onMethod = 'on' + name.slice(0, 1).toUpperCase() + name.slice(1);
          // 在methods里调用这个方法
          methods[onMethod] && methods[onMethod]();
        }
      };
    });
  }
}
const fsm = new StateMachine({
  init: 'solid',
  transitions: [
    {
      name: 'melt',
      from: 'solid',
      to: 'liquid',
    },
    {
      name: 'freeze',
      from: 'liquid',
      to: 'solid',
    },
    {
      name: 'vaporize',
      from: 'liquid',
      to: 'gas',
    },
    {
      name: 'condense',
      from: 'gas',
      to: 'liquid',
    },
  ],
  methods: {
    onMelt: function () {
      console.log('I melted');
    },
    onFreeze: function () {
      console.log('I froze');
    },
    onVaporize: function () {
      console.log('I vaporized');
    },
    onCondense: function () {
      console.log('I condensed');
    },
  },
});
fsm.melt();
fsm.vaporize();
```
