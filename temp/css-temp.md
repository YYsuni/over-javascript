box-sizing:border-box

盒子的宽高将会固定，不会受到内部内容、内边距的影响。

边框

```
border-style
border-width
```

也支持 border-top-width、border-bottom-style，但是这些写法不常见。常见写法为：

```css
border-top: double 10px red;
```

四个边一起写：

```css
border: 1px solid red;
```

轮廓线(不占据位置！)：

```css
outline-style: double;
outline-width: 5px;
outline-colr: red;

outline: solid 20px #ddd;
```

图片最大宽度高度等，如设置了 min-width，当元素实际宽度没有 min-width 宽，则采用 min-width 的值

```css
min-width
max-height
```

填充可用空间：

```css

```
