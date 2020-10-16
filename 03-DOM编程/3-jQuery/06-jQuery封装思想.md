# 06-jQuery 思想

## 一 编程思想

### 1.1 隐式迭代

> jQuery 的隐式迭代：jQuery 会在内部对每个匹配到的元素执行相应操作

jQuery 的选择器具备隐式迭代的特性，如下案例所示：

```html
<div>div1</div>
<div>div2</div>
<div>div3</div>

<script>
  let div = $("div");
  console.log($("div")); // 3个全拿到了
  div.css("background", "red"); // 3个div全部被修改为了red
</script>
```

**获取索引号方式：`jQuery对象.index()`**

由于隐式迭代在方法的内部会为匹配到的所有元素进行循环遍历，执行相应的方法；而不用我们再进行循环，简化我们的操作，方便我们调用。
如果获取的是多元素的值，大部分情况下返回的是第一个元素的值。
但是有时候我们需要对获取的元素集合中每个元素做不同的处理，可以使用 each()方法：

```js
$("li").each(function (i, elem) {
  //i：下标 elem : 每个元素
  $(elem).html(i);
});
```

### 1.2 排他思想

示例：

```html
<div style="background-color: aqua;">div1</div>
<div style="background-color: aqua;">div2</div>
<div style="background-color: aqua;">div3</div>
<script src="https://libs.baidu.com/jquery/1.11.3/jquery.min.js"></script>
<script>
  // 当前元素变化背景，其余去掉颜色
  $("div").click(function () {
    //  this 必须转换为 jQuery对象
    $(this).css("background", "green");
    $(this).siblings("div").css("background", "");
  });
</script>
```

### 1.3 链式编程

链式编程原理是 `return this;`。

通常情况下，只有设置操作才能把链式编程延续下去。因为获取操作的时候，会返回获取到的相应的值，无法返回 this。

## 二 框架封装思想

### 2.1 多库共存

多库共存指的是：jQuery 占用了$ 和jQuery这两个变量。当在同一个页面中引用了jQuery这个js库，并且引用的其他库（或者其他版本的jQuery库）中也用到了$或者 jQuery 这两个变量，那么，要保证每个库都能正常使用，这时候就有了多库共存的问题。

```js
// 模拟另外的库使用了 $ 这个变量，就与jQuery库产生了冲突
let $ = { name : “itecast” };
//noConflict() 可以让jQuery释放对$的控制权，让其他库能够使用$符号，此后，只能使用jQuery来调用jQuery提供的方法
let myQuery = $.noConflict(); // jQuery解决方案：开发者自定义jQuery的名称
let $ = 10;
myQuery(function(){
    myQuery('body').css('background','red');
});
```

### 2.1 插件机制

jQuery 只提供了 DOM 等操作，复杂的操作依赖于大量的第三方插件，我们可以称之为 jQuery 的生态。

通过插件的方式，可以扩展 jQuery 的功能，常用的插件网址有：

- <http://www.htmleaf.com>
- <http://www.jq22.com>

案例：

```js
<script>
$.extend({
    leftTrim: function(str){
        return str.replace(/^\s+/,'');
    },
    rightTrim: function(){
        //方法体
    }
});
</script>

<script>
let str = ' hello ';
console.log($.leftTrim(str));
</script>
```

比较实用的 jQuery 插件是：

- 瀑布流插件
- 懒加载插件 EasyLazyLoad.js：图片需要加载的时候才会显示，以提升性能
- 全屏滚动插件 fullpage.js
