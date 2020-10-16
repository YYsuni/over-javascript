## 一 布局简介

### 1.1 布局稳定性

如果要对一个盒子进行布局，使用 width、padding、margin 都可以改变其位置，但是在实际开发中，推荐的优先级为：

```txt
width > padding > margin
```

因为 margin 会有外边距合并、IE6 下 margin 加倍 bug，padding 则会影响盒子大小，需要加减计算，width 较为直观。

### 1.2 版心与通栏

**版心**：即可视区，指网页中主题内容所在的区域，一般在浏览器窗口水平居中显示，常见的宽度值是 960px,980px,1000px,1200px。

**通栏**：不设宽度的 div 很容易只做通栏，但在很多场景下，通栏时，内部的文字图片内容仍然是和版心位置一致的，比如常见的导航。

**常见实现方案**：导航通栏，导航内部设置一个盒子和版心一致。

## 二 对齐

让内容水平居中：

```css
text-align: center;
```

让块级元素水平居中：

```css
margin: 0 auto;

/* 或者写作 */
margin: auto;

/* 或者写作 */
margin-left: atuo;
margin-right: auto;
```

让行内元素、行内块元素实现对齐，如图片与文字底线对齐：

```css
    <div>
        <img src=''>
        文字...
    </div>
    vertical-align: center;     /* 默认值为baseline，即图片与文字的基线对齐，middle可以实现文字图片垂直居中显示 */
```

这里要注意的是：由于图片默认与文字的基线对齐，所以文字的底线会超出图片的位置，造成图片的底部出现空白区域，设置 vertical 的值为非基线（如 bottom）对齐即可，当然也可以将元素直接修改为块级元素来解决该 bug，因为块级元素不支持 vertical！

非标准流居中(即浮动、定位造成的脱标，`margin:auto`失效)：

```css
left: 50%; /* 先让盒子距离左侧增加50% */
margin-left: -100px; /* 再让盒子往左走自己宽度的一半 */
```

## 三 内外边距误区

### 3.1 平级盒子垂直方向外边距合并

```txt
<div class="div1">div1</div>
<div class="div2">div2</div>

.div1 { width: 100px; height: 100px; background: aqua; margin-bottom: 100px; }
.div2 { width: 100px; height: 100px; background: yellowgreen; margin-top: 100px;
}
```

此时 div1 和 div2 的边距并不是 200px，而时 100px，这是浏览器本身的问题，一般开发中直接避免同时使用外边距即可。

### 3.2 父子盒子垂直方向外边距塌陷

如图所示，开发中经常遇到图中父子嵌套的盒子，要求子盒子往下移动一点距离，可以修改父盒子的内边距或者修改子盒子的外边距：

![外边距塌陷01](/images/CSS/css-03.png)

如果添加外边距 margin-top: 100px;，那么会造成下面意向不到的效果：父子盒子一起往下移动了。

![外边距塌陷02](/images/CSS/css-04.png)

注意：外边距塌陷只会存在于垂直外边距上。

解决方案：

- 方案一：给父盒子指定 1 像素的上边框或者内边距
- 方案二：给父盒子添加 overflow:hidden(触发 bfc)

完整代码：

```txt
<div class="father">
  <div class="son">son div</div>
</div>

<style>
  .father {
    width: 300px;
    height: 300px;
    background: aqua;
    margin-bottom: 100px;
    overflow: hidden;
  }
  .son {
    width: 100px;
    height: 100px;
    background: yellowgreen;
    margin-top: 100px;
  }
</style>
```

### 3.3 子盒子内边距没有撑开父盒子

 很多时候，我们需要如下场景 ：子盒子 son 的内容往右移动一点：

![子盒子问题](/images/CSS/css-04.png)

常见的做法是：给子盒子内边距，但是我们会担心给了子盒子内边距是否会撑开父盒子。

```txt
<div class="father">
  <div class="son">son div</div>
</div>

<style>
  .father {
    width: 200px;
    height: 200px;
    background: aqua;
  }
  .son {
    background: yellowgreen;
    padding-left: 50px;
  }
</style>
```

事实上没有撑开父盒子，因为子盒子没有宽度，默认等于父盒子宽度。

## 四 导航栏布局

导航栏一般推荐使用 li 与 a 两种标签配合的方式书写，如下所示：

```css
<style>
  * {
  margin: 0;
  padding: 0;
}

.nav {
  width: 760px;
  height: 32px;
  margin: 0 auto;
  background-color: lightblue;
}

.nav ul li {
  float: left;
  list-style-type: none;
}

.nav ul li a {
  display: block;
  width: 80px;
  height: 32px;
  line-height: 32px;
  text-align: center;
  text-decoration: none;
}
</style>

<div class="nav">
  <ul>
    <li> <a> 首页</a> </li>
    <li> <a> 新闻</a> </li>
    <li> <a> 关于</a> </li>
  </ul>
</div>
```

这样做可以使语义更加清晰，而且在直接使用 a 元素时，容易让搜索引擎认为有堆砌关键字嫌疑。

## 五 margin 负值的应用

多个盒子之间相邻，合理设置 margin，可以不会造成一个边框过粗：

```css
div {
  float: left; /* 浮动的盒子是仅仅贴在一起的，第一个盒子向左1px，先造成后续所有盒子的浮动贴在一起，然后才陆续向左移动1px */
  width: 200px;
  height: 300px;
  border: 1px solid #ccc;
  margin-left: -1px;
  margin-top: -1px;
}

div :hover {
  border: 1px solid #f40;
  position: relative; /* 鼠标移入时，需要显示边框，不设定位，部分div边框显示不全 */
}
```

若 div 盒子本身已经是定位的了，则可以如下所示书写：

```css
div {
  position: relative; /* 鼠标移入时，需要显示边框，不设定位，部分div边框显示不全 */
  float: left; /* 浮动的盒子是仅仅贴在一起的，第一个盒子向左1px，先造成后续所有盒子的浮动贴在一起，然后才陆续向左移动1px */
  width: 200px;
  height: 300px;
  border: 1px solid #ccc;
  margin-left: -1px;
  margin-top: -1px;
}

div :hover {
  border: 1px solid #f40;
  z-index: 1; /* 由于div都是定位盒子，需要使用z-index实现层级效果 */
}
```
