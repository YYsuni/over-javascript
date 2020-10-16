# jQuery 基础

## 一 jQuery 初步使用

### 1.1 jQuery 简介

原生 JS 痛点：

- `window.onload` 事件只能出现一次，多次出现会覆盖之前的事件
- 兼容性复杂
- 简单功能原生实现复杂（比如各种循环，jQuery 隐式迭代帮我们做了）
  jQuery 的 API 都是方法，即要加小括号()，小传入的参数不同，功能不同。

```txt
版本一：1.x版本，兼容IE6/7/8
版本二：2.x版本，不兼容IE6/7/8
版本三：3.x版本，更精简，不再兼容低版IE
```

### 1.2 jQuery 使用步骤

```js
<script src="./jQuery-1.11.3.min.js"></script> <!-- 引包 -->
<script>
    $(document).ready(function () { //入口函数
        //代码
    });
    //也可以简写为：
    $(function(){
        $('#div').click(function () {
            console.log(1)
        });
    });
</script>
```

注意：

- `$` 实际上表示的是一个函数，即 jQuery 函数： `jQuery ===$`;
- jQuery 事件不带 on

### 1.3 jQuery 入口函数与 JS 入口函数

JS 的入口函数是：`window.onload = function() { }`;jQuery 的入口函数是：`$(function(){ })`;

区别：

- JS 入口函数只能出现一次，出现多次会存在事件覆盖的问题，jQuery 入口函数可以书写多次，没有覆盖问题。
- JS 入口函数是在所有的 文件资源 加载完成后才执行，jQuery 入口函数在**文档**加载完后执行，即无需外部资源加载，DOM 树加载完成就执行

### 1.4 jQuery 对象和 DOM 对象

使用 jQuery 选择器获取到的对象与原生的 DOM 对象是有区别的，**jQuery 获取的元素是对 DOM 对象包装后的伪数组**。

jQuery 这样做的目的是：封装后不需要大量重复的遍历，能够更加简便的实现兼容问题。

DOM 对象与 jQuery 对象转换：

```js
// DOM对象转换为jQuery对象：
$(DOM对象);

// jQuery对象转换为DOM对象：
let btn1 = jQuery对象[0]; //推荐使用该方式
let btn2 = jQuery对象.get(0); // 方式二
```

需要将 jQuery 对象转换回原生对象的原因：一些成员 jQuery 并未包装，比如多媒体的 `play()`方法。
