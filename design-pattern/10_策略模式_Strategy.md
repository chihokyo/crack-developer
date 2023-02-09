# 策略模式 Strategy

策略模式 (Strategy Pattern)又称政策模式，其定义一系列的算法，把它们一个个封装起来，并且使它们可以互 相替换。封装的策略算法一般是独立的，策略模式根据输入来调整采用哪个算法。关键是策略的实现和使用分离。

我感觉策略模式就是分离具体的算法到类。

> 条条大路通罗马。去机场，我能坐飞机，骑自行车，走路。
>
> 同样的年终奖。老板，员工，经理肯定都是不一样的
>
> 同样的支付。支付宝，微信，信用卡等等。
>
> 这就是差不多吧，反正都能找到结果，但是过程不一样。只要大量用到if-else的这种。都可以用策略模式来修改。

## 具体角色

- 上下文 用来控制策略的，操作中心
- 抽象策略
- 具体策略

> 总体来说，感觉和状态模式很像啊。

## 没有策略模式的时候

这个最明显的就是好多`ifelse`

```js
class Customer {
  constructor(type) {
    this.type = type;
  }

  pay(price) {
    if (this.type === 'normal') {
      return price;
    } else if (this.type === 'member') {
      return price * 0.8;
    } else if (this.type === 'vip') {
      return price * 0.5;
    }
    return price;
  }
}

let c1 = new Customer('normal');
console.log(c1.pay(100)); // 100
let c2 = new Customer('member');
console.log(c2.pay(100)); // 80
let c3 = new Customer('vip');
console.log(c3.pay(100)); // 50
```

## JS 实现

当有了策略之后，就是把这些上面的`return price` 都拉出来一个类，放到类里面去了。

```js
// 使用了策略之后呢
class Customer {
  constructor(type) {
    this.type = type;
  }

  cost(price) {
    return this.type.discount(price);
  }
}

class Kind {
  discount() {}
}

class Normal extends Kind {
  discount(price) {
    return price;
  }
}
class Member extends Kind {
  discount(price) {
    return price * 0.8;
  }
}
class Vip extends Kind {
  discount(price) {
    return price * 0.5;
  }
}

let c1 = new Customer(new Normal());
console.log(c1.cost(100));
let c2 = new Customer(new Member());
console.log(c2.cost(100));
let c3 = new Customer(new Vip());
console.log(c3.cost(100));
```

其实 js 里面，策略还可以内置在内部。

```js
class Customer {
  constructor() {
    this.types = {
      normal: function (price) {
        return price;
      },
      member: function (price) {
        return price * 0.8;
      },
      vip: function (price) {
        return price * 0.5;
      },
    };
  }
  cost(type, price) {
    return this.types[type](price);
  }
}

let c = new Customer();
console.log(c.cost('normal', 100));
console.log(c.cost('member', 100));
console.log(c.cost('vip', 100));
```

> 最后调用的时候，直接指定具体的策略（算法）就可以了。

## TS 实现

```ts
export {};
/**
 * 上下文
 */
class Context {
  // 通用的策略
  private strategy: Strategy;

  // 初始化策略
  constructor(strategy: Strategy) {
    this.strategy = strategy;
  }
  // 设置这个策略
  public setStrategy(strategy: Strategy) {
    this.strategy = strategy;
  }

  public doSomeBusinessLogic(): void {
    //逻辑操作
    const result = this.strategy.doAlgorithm(['a', 'b', 'c', 'd', 'e']);
    console.log(result.join(','));
  }
}

// 抽象的策略类
interface Strategy {
  doAlgorithm(data: string[]): string[];
}

// 具体的策略类A
class ConcreteStrategyA implements Strategy {
  public doAlgorithm(data: string[]): string[] {
    return data.sort();
  }
}

// 具体的策略类B
class ConcreteStrategyB implements Strategy {
  public doAlgorithm(data: string[]): string[] {
    return data.reverse();
  }
}

const context = new Context(new ConcreteStrategyA());
context.doSomeBusinessLogic(); // 这里用的A策略 也就是a,b,c,d,e

context.setStrategy(new ConcreteStrategyB()); // 这里转换成了新策略
context.doSomeBusinessLogic(); // 倒序了 e,d,c,b,a
```

## 应用场景

平常用的 jquery 的库就是

```html
!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
    <style>
      #content {
        width: 100px;
        height: 100px;
        background-color: green;
      }
    </style>
  </head>
  <body>
    <div id="content"></div>
    <button id="bigger">变大</button>
    <script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
    <script>
      $('#bigger').click(function () {
        $('#content').animate(
          { width: '200px', height: '200px' },
          1000,
          'linear'
        );
      });
    </script>
  </body>
</html>
```

```js
$('#content').animate({width:'200px',height:'200px'},1000,'linear');
});
```

**通用的表单校验（重点）**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>策略模式_表单校验</title>
  </head>
  <body>
    <form id="userform">
      用户名 <input type="text" name="username" /> 密码
      <input type="text" name="password" /> 手机号
      <input type="text" name="mobile" />
      <input type="submit" />
    </form>

    <script>
      const Validator = (function () {
        // 定义了很多策略
        let rules = {
          notEmpty(val, msg) {
            if (val == '') {
              return msg;
            }
          },
          minLength(val, length, msg) {
            if (val == '' || val.length < length) {
              return msg;
            }
          },
          maxLength(val, length, msg) {
            if (val == '' || val.length > length) {
              return msg;
            }
          },
          isMobile(val, msg) {
            if (!/^1\d{10}/.test(val)) {
              return msg;
            }
          },
        };

        function addRule(name, rule) {
          rules[name] = rule;
        }
        let checks = [];
        function add(element, rule) {
          checks.push(function () {
            // 相当于拿到策略（算法）的名字
            let name = rule.shift();
            // 增加这个值到前面
            rule.unshift(element.value);
            // 当有这个策略的时候，加入到
            return rules[name] && rules[name].apply(element, rule);
          });
        }
        function start() {
          for (let i = 0; i < checks.length; i++) {
            let check = checks[i];
            let msg = check();
            if (msg) {
              return msg;
            }
          }
        }
        return {
          add,
          start,
          addRule,
        };
      })();

      let form = document.getElementById('userform');
      debugger;
      form.onsubmit = function () {
        Validator.add(form.username, ['notEmpty', '用户名不能为空']);
        Validator.add(form.password, ['minLength', 6, '密码小于6位最少长度']);
        Validator.add(form.password, ['maxLength', 8, '密码大于8位最大长度']);
        Validator.add(form.mobile, ['isMobile', '手机号不合法']);
        let msg = Validator.start();
        if (msg) {
          alert(msg);
          return false;
        }
        alert('校验通过');
        return true;
      };
    </script>
  </body>
</html>
```

上面一段逻辑最难的就是，理解闭包和 IIFE。

## 策略状态区别

你可以看到，策略和状态都很相近。

- 都有一个上下文
- 策略模式是不同的策略（算法）
- 状态模式是不同的状态

区别就是

策略 → 你提供的具体的校验规则。你主动提供。

状态 → 自己内部就转换了，你直接命令他就自己做了。内部状态的变化，你还要自己指定从哪里到哪里？不需要的。

当你不知道一种模式的区别的时候，就看调用是怎么调用的。
