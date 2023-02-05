# Adapter 适配器模式

这个模式我感觉很像什么呢，就是新建一个中间件的感觉。和 A 和 B 互相不能适配的时候，新建一个 C。然后当成中介，对 A 和 B 进行转换。A→**C**→B 这个的感觉。

## 登场角色

- Adaptee （源角色 source）也就是图上的 A
- Target （目标）也就是图上的 B
- Adapter（适配器）→ 主要的逻辑 也就是图上的 C
- Client 最后就是客户端了

差不多出场角色也就是这样的。

## JS 实现

```js
const chinaPlug = {
  type: 'china plus',
  chinaInPlug() {
    console.log('china charging now');
  },
};

const japanPlug = {
  type: 'japan plus',
  japanInPlug() {
    console.log('japan charging now');
  },
};

function cnToJp(plug) {
  return {
    chinaInPlug() {
      return plug.japanInPlug();
    },
  };
}

cnToJp(japanPlug).chinaInPlug();
```

## TS 的表现

```ts
// 这里是我们需要的
class Target {
  public request(): string {
    return "Target: The default target's behavior.";
  }
}

// 这里可以简单的理解成source
class Adaptee {
  public specificRequest(): string {
    return '.eetpadA eht fo roivaheb laicepS';
  }
}

class Adapter extends Target {
  private adaptee: Adaptee;
  constructor(adaptee: Adaptee) {
    super();
    this.adaptee = adaptee;
  }

  public request(): string {
    // 调用原来的so
    const result = this.adaptee.specificRequest().split('').reverse().join('');
    return `Adapter: (TRANSLATED) ${result}`;
  }
}

function clientCode(target: Target) {
  console.log(target.request());
}
// 目标本身OK的
const target = new Target();
console.log(clientCode(target));

// before 这里测试一个没转换的 不知道在说些什么
const adaptee = new Adaptee();
console.log(`Adaptee: ${adaptee.specificRequest()}`);

// after 来适配一下
const adapter = new Adapter(adaptee);
clientCode(adapter);
```

## 使用场景

感觉适配器模式总是在批量转换接口的时候用，比如现在用 ajax，想改成 fetch 的话，改成 axios 这种的时候就可以用上。

现在写一个是伪代码一样的感觉

```js
// 这是老逻辑
function Ajax(type, url, data, success, failed) {}
```

这个时候，我想用新的接口

```js
// 但现在不想用这个了
// 想用新的逻辑
function AjaxAdapter(type, url, data, success, failed) {
  // 这里开始写具体的逻辑，只不过转换成fetch or axios
}
async function Ajax(type, url, data, success, failed) {
  await AjaxAdapter(type, url, data, success, failed);
}
```

下面是用 jquery 可以转换的

```js
// 下面不是伪代码，而是实际可以用的
// 这是原来的ajax模式
$.ajax({
  url,
  type: 'POST',
  dataType: 'json',
  data: { id: 1 },
}).then(function (data) {
  console.log(data);
});

// 这里相当于重写了
window.$ = {
  ajax(options) {
    return fetch(options.url, {
      method: options.type || 'GET',
      body: JSON.stringify(options.data || {}),
    }).then((response) => response.json());
  },
};
```

下面也是一个应用场景

把 fs.readFile 异步回调封装成一个返回 Promise 的形式。

```js
// 下面也是一个应用场景，相当于把一个异步的场景转换成可以返回Promise的场景
const fs = require('fs');
function promisify(fn) {
  return function (...rest) {
    return new Promise(function (resolve, reject) {
      fn(...rest, function (err, data) {
        if (err) {
          reject(err);
        } else {
          resolve(data);
        }
      });
    });
  };
}

// fs.readFile本来是一个异步方法
let readFile = promisify(fs.readFile);
(async function read() {
  let one = await readFile('1.txt', 'utf8');
  let two = await readFile('2.txt', 'utf8');
  let three = await readFile('3.txt', 'utf8');
  console.log(one, two, three);
})();
```
