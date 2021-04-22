# 常用 css 片段

## 清除浮动 Clearfix

```css
.clearfix::after {
  content: "";
  display: block;
  clear: both;
}
```

## 单行溢出省略

```css
.ellipsis {
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
  /* 超出200px */
  width: 200px;
}
```

## 多行溢出省略

```css
.ellipsis {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  /* 行数 */
  -webkit-line-clamp: 2;
  overflow: hidden;
}
```

## Footer 自动置底

当内容加上页脚不足以撑满整个页面 底部留白会比较难看的时候 我们可以使用 footer 自动置底

```css
body,
html {
  height: 100%;
}
/* 容器盒子减去底部高度*/
.content {
  min-height: calc(100vh - 100px);
}
.footer {
  height: 100px;
}
```

布局

```html
<div class="content">内容盒子</div>
<!-- 将会自动置底 -->
<div class="footer">底部盒子</div>
```

<!-- ## 高度溢出隐藏
当高度未知时 将元素的高度从 0 转换为自动



```html

``` -->
