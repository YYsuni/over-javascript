## 一 改变成员特征

对象的成员在 JS 引擎中有着各种特征，比如：是否可访问等。这些特征并不能让开发者直接访问：

- Configurable：默认值为 true，表示能否通过 delete 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。
- Enumerable：默认值为 true，表示能否通过 for-in 循环返回属性
- Writable：默认值为 true，表示能否修改属性的值
- Value：默认值为 false，包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置

示例：

```js
var p = {
  // 直接在对对象上定义了属性，则 Configurable、Enumerable、Writable都被默认设置为了true
  name: "lisi", // name属性的  Value 特征被设置为了 lisi
};
```

对象成员的这些默认特征如果需要修改，需要借助 ES5 的 `Object.defineProperty()` 方法，该方法接收三个参数：

- 属性所在对象
- 属性的名字
- 描述符，包括：configurable、 enumerable、 writable 和 value。

示例：

```js
var person = {};

Object.defineProperty(person, "name", {
  writable: false,
  configurable: false,
  value: "lisi",
});

console.log(person.name); // lisi

// 这里仍然是 lisi，并未改变，因为该属性是只读的，而且在严格模式下，该操作会直接报错
person.name = "zs";
console.log(person.name); // lisi

// 类似的规则同样适用于 delete，因为 configurable 为 false，不允许删除
delete p.name;
console.log(person.name); // lisi
```

注意：

- defineProperty 方法可以被多次调用，但是一旦设置 configurable 设置为 false 之后就不能再调用了！
- defineProperty 方法如果不指定内部属性，默认都是 false

## 二 改变访问器属性

访问器属性是一对 getter、setter 函数（非必须），分别用于读取属性、写入属性。访问器属性也有四个特征：

- Configurable：默认值为 true，表示能否通过 delete 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。
- Enumerable：默认值为 true，表示能否通过 for-in 循环返回属性
- Get：在读取属性时调用的函数，默认值为 undefined
- Set：在写入属性时调用的函数，默认值为 undefined

示例：

```js
var book = {
  _year: 2004,
  edition: 1,
};

Object.defineProperty(book, "year", {
  get: function () {
    return this._year;
  },
  set: function (newValue) {
    if (newValue > 2004) {
      this._year = newValue;
      this.edition += newValue - 2004;
    }
  },
});

book.year = 2005;
console.log(book.edition); //2
```

不一定非要同时指定 getter 和 setter。只指定 getter 意味着属性是不能写，尝试写入属性会被忽略。在严格模式下，尝试写入只指定了 getter 函数的属性会抛出错误。类似地，只指定 setter 函数的属性也不能读，否则在非严格模式下会返回 undefined，而在严格模式下会抛出错误。

在 IE 中，只有 9 及以上版本才支持，如果是旧版，一 般 都 使 用 两 个 非 标 准 的 方 法 ：`__defineGetter__()`和`__defineSetter__()`:

```js
var book = {
  _year: 2004,
  edition: 1,
};
//定义访问器的旧有方法
book.__defineGetter__("year", function () {
  return this._year;
});
book.__defineSetter__("year", function (newValue) {
  if (newValue > 2004) {
    this._year = newValue;
    this.edition += newValue - 2004;
  }
});
book.year = 2005;
console.log(book.edition); //2
```

## 三 定义多个属性

为了方便开发，JS 也提供了同时定义多个属性的方法`Object.defineProperties()`：

```js
var book = {};
Object.defineProperties(book, {
  _year: {
    value: 2004,
  },
  edition: {
    value: 1,
  },
  year: {
    get: function () {
      return this._year;
    },
    set: function (newValue) {
      if (newValue > 2004) {
        this._year = newValue;
        this.edition += newValue - 2004;
      }
    },
  },
});
```

## 四 读取属性特性

用 ECMAScript 5 的 Object.getOwnPropertyDescriptor()方法，可以取得给定属性的描述符。这个方法接收两个参数：属性所在的对象和要读取其描述符的属性名称。返回值是一个对象，如果是访问器属性，这个对象的属性有 configurable、 enumerable、 get 和 set；如果是数据属性，这个对象的属性有 configurable、 enumerable、 writable 和 value。例如：

```js
var book = {};
Object.defineProperties(book, {
  _year: {
    value: 2004,
  },
  edition: {
    value: 1,
  },
  year: {
    get: function () {
      return this._year;
    },
    set: function (newValue) {
      if (newValue > 2004) {
        this._year = newValue;
        this.edition += newValue - 2004;
      }
    },
  },
});

var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
alert(descriptor.value); //2004
alert(descriptor.configurable); //false
alert(typeof descriptor.get); //"undefined"

var descriptor = Object.getOwnPropertyDescriptor(book, "year");
alert(descriptor.value); //undefined
alert(descriptor.enumerable); //false
alert(typeof descriptor.get); //"function"
```
