# Builder 建造者模式

所谓万丈高楼平地起，但是我们建造（Build）高楼时，需要经历很多阶段，比如打地基、搭框架、浇筑水泥、封顶等，这些都是很难一气呵成的。所以一般我们是先建造组成高楼的各个部分，然后将其一个个地组装起来，好比搭积木一般，分阶段拼接后组装成一个完整的物体。还有个问题，就是同样的积木，同样的搭建过程，却能 Build 出不同的物体，这就叫做建造者模式。

这个的重点在于什么呢？在于像搭积木一样的 new 出一个实例。比如造房子，造车子这种特别复杂需要**一步步**来实现的场景。

写完这篇文章之后，我告诉自己一定要记住四个角色

> - Director
> - Builder
> - ConcreateBuilder
> - Product

## Java 实现

由于在 JS 里目前没看到。所以先套用一下 Java 的。在没有用建造者模式的时候

```java
/**
 * 建造者模式进化前 before
 * 抽象基类
 */
public abstract class AbstractHouse {

    public abstract void buildBasic();
    public abstract void buildWalls();
    public abstract void roofed();

    public void build() {
        buildBasic();
        buildWalls();
        roofed();
    }
}

package com.design.pattern.builder.before;

/**
 * 建造者模式进化前 before
 * 具体实现 CommonHouse 普通房子
 */
public class CommonHouse extends AbstractHouse {
    @Override
    public void buildBasic() {
        System.out.println("CommonHouse buildBasic...");
    }

    @Override
    public void buildWalls() {
        System.out.println("CommonHouse buildWalls...");
    }

    @Override
    public void roofed() {
        System.out.println("CommonHouse roofed...");
    }

}

/**
 * 建造者模式进化前 before
 * 具体实现 HighHouse 高楼
 */
public class HighHouse extends AbstractHouse {

    @Override
    public void buildBasic() {
        System.out.println("HighHouse buildBasic...");
    }

    @Override
    public void buildWalls() {
        System.out.println("HighHouse buildBasic...");
    }

    @Override
    public void roofed() {
        System.out.println("HighHouse buildBasic...");
    }

}

// 调用开始了

/**
 * 开始建造
 */
public class ClientMain {
    public static void main(String[] args) {
        System.out.println("建造一个普通房子");
        CommonHouse ch = new CommonHouse();
        ch.build();
        System.out.println("建造一个高房子");
        HighHouse hh = new HighHouse();
        hh.build();
    }
}

```

> 可以看出上面只是搞了一个父类作为 base，同时父类还有一个构建方法。到了子类才具体有了实现。

用了建造者模式呢？

这里的出场人物就比较多了。我感觉就像是手机组装厂一样。

- Director → Apple 公司自己造手机吗？不是。他只是设计而已。最后做整合。
- Builder → 大家制造的时候都遵守的标准（抽象的 Builder），供应链（具体的 ConcreateBuilder）才是真正的制造者。
- Product → 具体的商品零部件就是商品，比如屏幕。
- Client → 也就是我们了，测试类而已。

具体代码太多，先不写。写把代码解析后的 UML 图放在下面了。⚠️ 此处少 Java 代码 因为太多了 ⚠️。

![image-20230119000130463](https://raw.githubusercontent.com/chihokyo/image_host/develop/image-20230119000130463.png)

```java
public class ClientMain {
    public static void main(String[] args) {

        System.out.println("建造一个普通房子");
        // 找一家供应商
        CommonBuilding commonBuilding = new CommonBuilding();
        // 把供应商放到Apple公司里
        HouseDirector houseDirector = new HouseDirector(commonBuilding);
      	// 然后Apple就提供了手机给你
        House house = houseDirector.constructHouse();
      	// 最后你拿到了产品
        System.out.println(house);
    }
}
```

> 🔥 重点，我觉得从调用方也就是上面。可以看到这个重要信息，我们需要 new 的只有供应商+Apple 公司。最后你就可以拿到产品。至于这个供应商怎么做的，如何做的。你都不用去思考，去在意。**如何做的用的是 ConcreateBuilder。** **帮你调度的是 Director**，也就是苹果。
>
> 就好比，你买 iPhone，选好了屏幕必须要 LG 的，然后给 Apple 下单，就能收到 iPhone 了。

和工厂模式好像啊，有什么区别吗？

> 建造者模式关注的是**零件类型和装配顺序**（工艺）同为创建型模式，注重点不同。另外工厂模式只有一个建造方法，而建造者模式有**多个建造零部件的方法并且强调建造顺序**，而工厂模式没有顺序的概念。
>
> 重点就是**顺序调度多零件**！

## JS

由于 JS 不是严格的 OOP。这里直接吗，没有了所谓的行规。直接就是具体的产品实现了。只有具体的**ConcreateBuilder**

```js
// ConcreateBuilder 建造者，汽车部件厂家，提供具体零部件的生产
function CarBuilder({ color = 'white', weight = 0 }) {
  this.color = color;
  this.weight = weight;
}

// ConcreateBuilder 生产部件，轮胎
CarBuilder.prototype.buildTyre = function (type) {
  const tyre = {};
  switch (type) {
    case 'small':
      tyre.tyreType = '小号轮胎';
      tyre.tyreIntro = '正在使用小号轮胎';
      break;
    case 'normal':
      tyre.tyreType = '中号轮胎';
      tyre.tyreIntro = '正在使用中号轮胎';
      break;
    case 'big':
      tyre.tyreType = '大号轮胎';
      tyre.tyreIntro = '正在使用大号轮胎';
      break;
  }
  this.tyre = tyre;
};

// ConcreateBuilder 生产部件，发动机
CarBuilder.prototype.buildEngine = function (type) {
  const engine = {};
  switch (type) {
    case 'small':
      engine.engineType = '小马力发动机';
      engine.engineIntro = '正在使用小马力发动机';
      break;
    case 'normal':
      engine.engineType = '中马力发动机';
      engine.engineIntro = '正在使用中马力发动机';
      break;
    case 'big':
      engine.engineType = '大马力发动机';
      engine.engineIntro = '正在使用大马力发动机';
      break;
  }
  this.engine = engine;
};

// Director 奔驰厂家，负责最终汽车产品的装配
function benChiDirector(tyre, engine, param) {
  var _car = new CarBuilder(param);
  _car.buildTyre(tyre);
  _car.buildEngine(engine);
  return _car;
}

/* Client 获得产品实例 */
const benchi1 = benChiDirector('small', 'big', {
  color: 'red',
  weight: '1600kg',
});

console.log(benchi1);
```

如果这个时候我们想给车增加一个空调怎么办？

```js
// 1. 新增空调建造方法
CarBuilder.prototype.buildAirCon = function (type) {
  const aircon = {};
  switch (type) {
    case 'small':
      aircon.airconType = '小空调';
      aircon.airconIntro = '正在使用小空调';
      break;
    case 'normal':
      aircon.airconType = '中空调';
      aircon.airconIntro = '正在使用中空调';
      break;
    case 'big':
      aircon.airconType = '大空调';
      aircon.airconIntro = '正在使用大空调';
      break;
  }
  this.aircon = aircon;
};

// 2. 多传入一个aircon参数
function benChiDirector(tyre, engine, aircon, param) {
  var _car = new CarBuilder(param);
  _car.buildTyre(tyre);
  _car.buildEngine(engine);
  // 3. 多建造
  _car.buildAirCon(aircon);
  return _car;
}
```

这里使用 ES6+链式调用修改一下

```js
class CarBuilder {
  constructor({ color = 'white', weight = '999' }) {
    this.color = color;
    this.weight = weight;
  }
  buildTyre(type) {
    const tyre = {};
    switch (type) {
      case 'small':
        tyre.tyreType = '小号轮胎';
        tyre.tyreIntro = '正在使用小号轮胎';
        break;
      case 'normal':
        tyre.tyreType = '中号轮胎';
        tyre.tyreIntro = '正在使用中号轮胎';
        break;
      case 'big':
        tyre.tyreType = '大号轮胎';
        tyre.tyreIntro = '正在使用大号轮胎';
        break;
    }
    this.tyre = tyre;
    // 这里返回this 可以进行链式调用
    return this;
  }

  buildAirCon(type) {
    const aircon = {};
    switch (type) {
      case 'small':
        aircon.airconType = '小空调';
        aircon.airconIntro = '正在使用小空调';
        break;
      case 'normal':
        aircon.airconType = '中空调';
        aircon.airconIntro = '正在使用中空调';
        break;
      case 'big':
        aircon.airconType = '大空调';
        aircon.airconIntro = '正在使用大空调';
        break;
    }
    this.aircon = aircon;
    return this;
  }
}

const benchi2 = new CarBuilder({ color: 'red', weight: '1600kg' })
  .buildTyre('small')
  .buildAirCon('big');
console.log(benchi2);
```

那么建造者模式适用于什么呢？

就是当你一个东西，构造函数的参数有很多的时候。例如下面这种

```js
class CarBuilder {
  constructor(engine, weight, height, color, tyre, name, type) {
    this.engine = engine;
    this.weight = weight;
    this.height = height;
    this.color = color;
    this.tyre = tyre;
    this.name = name;
    this.type = type;
  }
}

const benchi = new CarBuilder(
  '大马力发动机',
  '2ton',
  'white',
  '大号轮胎',
  '奔驰',
  'AMG'
);
```

- 这样很多，容易写错不说。
- 全部耦合在一起了。语义化也不明显

```js
// 通过链式调用，解决了写在一起的尴尬
class CarBuilder {
  constructor(engine, weight, height, color, tyre, name, type) {
    this.engine = engine;
    this.weight = weight;
    this.height = height;
    this.color = color;
    this.tyre = tyre;
    this.name = name;
    this.type = type;
  }

  setCarProperty(key, value) {
    if (Object.getOwnPropertyNames(this).includes(key)) {
      this[key] = value;
      return this;
    }
    throw new Error(`key error : ${key}不是实例的属性`);
  }
}

const benchi = new CarBuilder()
  .setCarProperty('engine', '大马力发动机')
  .setCarProperty('weight', '2ton')
  .setCarProperty('height', '2000mm')
  .setCarProperty('color', 'white')
  .setCarProperty('tyre', '大号轮胎')
  .setCarProperty('name', '奔驰')
  .setCarProperty('type', 'AMG');
console.log(benchi);
```

> 如果觉得大家都是叫`setCarProperty()` 这样语义化不明显，还可以继续优化

```js
class CarBuilder {
  constructor(engine, weight, height, color, tyre, name, type) {
    this.engine = engine;
    this.weight = weight;
    this.height = height;
    this.color = color;
    this.tyre = tyre;
    this.name = name;
    this.type = type;
  }

  setPropertyFuncChain() {
    Object.getOwnPropertyNames(this).forEach((key) => {
      const functionName =
        //这里就是相当于engine变成setEngine
        'set' + key.replace(/^\w/g, (str) => str.toUpperCase());
      this[functionName] = (value) => {
        this[key] = value;
        return this;
      };
    });
    return this;
  }
}

const benchiChain = new CarBuilder()
  // 这个方法就是增加了属性，这个属性是setXXX的方法
  .setPropertyFuncChain()
  // 这样你传入的时候就相当于调用的是setXXX方法也就是
  // (value) => {
  // this[key] = value;
  // return this};
  .setEngine('大马力发动机')
  .setWeight('2ton')
  .setHeight('2000mm')
  .setColor('white')
  .setTyre('大号轮胎')
  .setName('奔驰')
  .setType('AMG');
```

下面还有一个 React 的建造者模式的应用，我感觉还是写的很好。相当于调用方式改变了。

```js
// before
class ContainerComponent extends Component {
  componentDidMount() {
    this.props.fetchThings();
  }
  render() {
    return <PresentationalComponent {...this.props} />;
  }
}

ContainerComponent.propTypes = {
  fetchThings: PropTypes.func.isRequired,
};

const mapStateToProps = (state) => ({
  things: state.things,
});
const mapDispatchToProps = (dispatch) => ({
  fetchThings: () => dispatch(fetchThings()),
  selectThing: (id) => dispatch(selectThing(id)),
  blowShitUp: () => dispatch(blowShitUp()),
});

export default connect(mapStateToProps, mapDispatchToProps)(ContainerComponent);

// after 可以看到从上面的分离，变成了下面的链式调用
// 说实话，我觉得链式调用好像也不是特别优雅的感觉
export default ComponentBuilder('ContainerComponent')
  .render((props) => <PresentationalComponent {...props} />)
  .componentDidMount((props) => props.fetchThings())
  .propTypes({
    fetchThings: PropTypes.func.isRequired,
  })
  .mapStateToProps((state) => ({
    things: state.things,
  }))
  .mapDispatchToProps((dispatch) => ({
    fetchThings: () => dispatch(fetchThings()),
    selectThing: (id) => dispatch(selectThing(id)),
    blowShitUp: () => dispatch(blowShitUp()),
  }))
  .build();
```

> 但是这个是怎么实现的呢？他的思路是什么？

![image-20230119154347339](https://raw.githubusercontent.com/chihokyo/image_host/develop/image-20230119154347339.png)

链式调用我感觉就是把产品+指挥者都写在了一起。然后加上一个建造者就可以。

还是要多写一些代码。

## TS

下面这一段，是我看这个网站写的模式，我觉得还是有参考价值的[TypeScript **生成器**模式讲解和代码示例](https://refactoringguru.cn/design-patterns/builder/typescript/example)

其实只要明确四个角色，产品 + 建造者（抽象+具体） + 指挥者 就不会出错的。

```typescript
// 产品：Product
class IPhone {
  public iPhoneParts: string[] = [];
  public listParts(): void {
    console.log(`Product parts: ${this.iPhoneParts.join(', ')}\n`);
  }
}
// 行规：Builder
interface Bulider {
  produceDisplay(): void;
  produceBattery(): void;
  produceCamera(): void;
}

// 具体供应链：ConcreteBuilderA
class ConcreteBuilderA implements Bulider {
  private iphone: IPhone = new IPhone();

  public reset(): void {
    this.iphone = new IPhone();
  }

  public produceDisplay(): void {
    this.iphone.iPhoneParts.push('加入LG屏幕');
  }
  public produceBattery(): void {
    this.iphone.iPhoneParts.push('加入华强北电池');
  }
  public produceCamera(): void {
    this.iphone.iPhoneParts.push('加入索尼屏幕');
  }

  public getIPhone(): IPhone {
    return this.iphone;
  }
}

// 指挥者：Director
class Apple {
  private builder!: Bulider;
  public setBuilder(builder: Bulider): void {
    this.builder = builder;
  }
  public buildIPhone(): void {
    // 想用什么顺序就用什么顺序
    this.builder.produceBattery();
    this.builder.produceCamera();
    this.builder.produceDisplay();
  }
}

// 客户想要手机了
function client(director: Apple) {
  const builder = new ConcreteBuilderA();
  director.setBuilder(builder);
  console.log('指定好了供应商 一切就绪');
  director.buildIPhone();
  const res = builder.getIPhone();
  console.log(res);
}

const director = new Apple();
client(director);
```
