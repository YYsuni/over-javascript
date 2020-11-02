## 一 理解对象的成员

### 1.1 对象的成员检测

使用 属性直接查询、in、hasOwnProperty()、propertyIsEnumerable()等方式可以检测对象中是否存在该成员。但是如果对象的属性是通过继承得到的，那么上述操作就会出现一些特殊情况：

```js
var father = {
  surname: "李",
  age: 30,
};

var son = inherit(father); // inherit是自定义的继承函数
son.age = 1;

// in 方式
console.log("age" in son); // true
console.log("surname" in son); // true
console.log("toString" in son); // true
console.log("toString" in Object.prototype); // true

// 是hasOwnProperty()方式：不会检查原型链
console.log(son.hasOwnProperty("age")); // true
console.log(son.hasOwnProperty("surname")); // false 继承字段无法识别
console.log(son.hasOwnProperty("toString")); // false 继承字段无法识别
console.log(Object.prototype.hasOwnProperty("toString")); // true

// propertyIsEnumerable()方式：
/*
propertyIsEnumerable()是hasOwnProperty()的增强版，只有检测到是自有属性，且可枚举型为true时，返回值才为true
可枚举性：JS代码创建的属性都是可枚举的，包括自有属性、继承属性都是可枚举的，但是可以使用特殊手段改变属性为不可枚举
*/
console.log(son.propertyIsEnumerable("age")); // true
console.log(son.propertyIsEnumerable("surname")); // false
console.log(son.propertyIsEnumerable("toString")); // false
console.log(Object.prototype.propertyIsEnumerable("toString")); // false
```

使用 `son.age !== undefined` 的方式也可以用来判断对象是否存在属性，作用与 in 类似，但是在一些场合，这种做法欠妥周全：

```js
var obj = { x: undefined }; // 对象属性被显式赋值了undefined
console.log(obj.x !== undefined); // false
console.log("x" in obj); // true
```

### 1.2 对象属性遍历

对象的属性遍历经常是会用 `for in`方式：

```js
var father = {
  surname: "李",
  age: 30,
  run: function () {
    console.log("run...");
  },
};

var son = inherit(father);
son.age = 1;

for (var item in son) {
  console.log(item); // age  run  surname
}
```

从上看出 for in 循环可以遍历对象中的可枚举属性（自有属性、继承属性等），对象内置方法不可枚举，但是这仅限于 ES5 及其之后标准。

通常需要多属性进行一些过滤操作：

```js
if (!item.hasOwnProperty(item)) {
  continue; // 跳过继承的属性
}

if (typeof son[item] === "function") {
  continue; // 跳过方法
}
```

ES5 额外提供了两个函数用来枚举属性：

- Object.keys()：返回数组，数组元素是可枚举的自有属性名称
- Object.getOwnPropertyNames()：返回所有自有属性名称

### 1.3 对象属性删除

delete 运算符用来删除对象属性，但是不会删除原型中的属性。

### 1.4 锁定对象

锁定键：preventExtensions 方法锁定对象后，添加新的键，不会产生影响，甚至在严格模式下，会报错。

封闭对象：seal()  方法可以直接彻底封闭对象。

冻结对象：freeze 方法。冻结后的对象彻底无法修改、删除。

```js
var host = {
  url: "localhost:8080/api",
  port: 443,
};

Object.freeze(host); // host已经无法变更
host.port = 446;
console.log(host.port); // 443

console.log(Object.isFrozen(host)); // true
```
