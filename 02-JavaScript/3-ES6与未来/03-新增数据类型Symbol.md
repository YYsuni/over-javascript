# 03-1-语法改进-新增数据类型 Symbol

## 一 新增数据类型 Symbol

Symbol 是 ES6 新增的**原始数据类型！**，所以在 ES6 中，JavaScript 的原始数据类型现在有**7 种**：

```txt
Number（ES5）：     数值类型，是基本类型，typeof() 返回 number
String（ES5）：     字符串类型，是基本类型，typeof() 返回 string
Boolean(ES5)：     布尔类型，是基本类型，typeof() 返回 true/false
Null（ES5）：       空类型，是基本类型，typeof() 返回 object，用于未知的值，是个只有 null 值的独立类型
Undefined（ES5）：  未定义类型，是基本类型，typeof() 返回 undefined，用于未定义的值，是个只有 undefined 值的独立类型
Symbol（ES6）：     是基本类型，typeof() 返回 symbol，用于唯一标识
Object（ES5）：     是引用类型，typeof() 返回 object，用于复杂数据结构
```

简单使用：

```js
let s = Symbol();
console.log(typeof s); // symbol

// Symbol 类型不是对象，不能像对象那样使用，只能用反射(`Reflect.ownKeys`)的方式获取其所有键
s.name = "zs"; // 不能像对象那样使用
console.log(s.name); // undefined
```

为了区分不同的 Symbol 变量，可以在构造时加入描述，使用示例：

```js
let s1 = Symbol("s..."); // 参数只是变量的描述
let s2 = Symbol("s...");
console.log(s1.description); // s...
console.log(s1 == s2); // false
```

Symbol 类型是一种类似字符串的数据类型，只是用来表示独一无二的值，产生的目的是为了解决命名冲突问题。该数据类型不能与其他数据进行运算。

`Symbol.for` 方法也可以用来声明一个 Symbol 类型，但是 for 函数会在内存中查找是否已有定义，会造成如下所示现象：

```js
let s3 = Symbol.for("s...");
let s4 = Symbol.for("s...");
console.log(s3 == s4); // true

// 使用 Symbol.for 定义的数据，还可以通过 keyFor 获取到其描述。不是通过 for 定义的获取的描述是undefined
console.log(Symbol.for(s3)); // s...
```

## 二 Symbol 的使用场景

```js
let users = {
  lisi: "30岁，前端工程师",
  lisi: "24岁，设计师",
};

// 这2个人的名字一样
console.log(users.lisi); // 24岁，设计师
```

如果要继续采用上述的书写格式：

```js
let user1 = {
  name: "lisi",
  key: Symbol(),
};

let user2 = {
  name: "lisi",
  key: Symbol(),
};

let userInfo = {
  [user1.key]: "30岁，前端工程师",
  [user2.key]: "24岁，设计师",
};
```

使用Symbol来控制对象中的成员无法被访问：

```js
const DATA = Symol();
const user = {
  [DATA]: {name},
  set name(v) {
    this[DATA].name = v;
  }
  get name(){
    return this[DATA].name;
  }
}
```

## 三 Symbol 成员

### 3.1 Symbol 成员获取

```js
let s = Symbol();

let p = {
  name: "lisi",
  [s]: "隐藏属性",
};

// 使用 for in 或者 for of 都无法遍历到 [s] 成员
// for (const key in p){}
// for (const key of Object.keys(p)){}

// 只能遍历到 Symbol类型属性
// for(const key of Object.getOwnPropertySymbols(p)){}

// 遍历所有属性
for (const key of Reflect.ownKeys(p)) {
  console.log(key); // name  Symbol()
}
```

### 3.2 Symbol 内置属性

ES6 提供了 11 个内置的 Symbol 值，如：

```js
Symbol.hasInstance; // 当其他兑现使用 instanceof 判断是否为该对象的实例时，调用该方法
Symbol.match; // 当执行 str.match(obj) 时，如果该属性存在，会调用该方法
```

示例：

```js
class Person {
  static [Symbol.hasInstance]() {
    console.log("被用来检测！");
  }
}

let o = {};
console.log(o instanceof Person);
```
