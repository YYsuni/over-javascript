## 一 Date 类型

ECMAScript 的 Date 类型使用 UTC 时间，即 1970 年 1 月 1 日午夜（零时）开始经过的毫秒数来保存日期。

Date 的使用示例：

```js
// 获取当前时间
var now = new Date();
console.log(now); //  2020-03-27T05:06:42.294Z

console.log(now.toLocaleString()); // 2020-3-27 13:10:31
console.log(now.toLocaleDateString()); // 2020-3-27
console.log(now.toLocaleTimeString()); // 13:10:31
```

Date 对象在创建时也可以传入日期参数，以构建该日期的对象（如果参数不能转变为一个日期，则返回 NaN）：

```js
// 写法一
var someDate = new Date(Date.parse("May 25, 2004"));

// 写法二
var someDate = new Date("May 25, 2004");
```

贴士：日期对象在不同浏览器中有不同的行为，如大部分浏览器遇到超过日期范围的值，会将日期替换为当前值，在解析"January 32, 2007"
时，有的浏览器会将其解释为"February 1, 2007"。而 Opera 则倾向于插入当前月份的当前日期，返回"January 当前日期， 2007"。

ES5 添加了 Date.now()方法，返回表示调用这个方法时的日期和时间的毫秒数：

```js
//取得开始时间
var start = Date.now();

//调用函数
doSomething();

//取得停止时间
var stop = Date.now(),
    result = stop – start;
```

Date 对象还有一些时间设置与获取方法：

```js
// 获取当前时间
var now = new Date();

console.log(now.getFullYear()); // 2020
console.log(now.getMonth()); // 2    其中0表示一月， 11表示十二月，类推
console.log(now.getDate()); // 27
console.log(now.getHours()); // 13

console.log(now.getTime()); // 时间戳

// 设置日期月份中的天数。如果传入的值超过了该月中应有的天数，则增加月份
now.setDate(33);
console.log(now.getMonth()); // 3
```
