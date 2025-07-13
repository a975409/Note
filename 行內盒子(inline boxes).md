預設CSS樣式如下：
```CSS
div {
  display: inline;
}
```

有以下特性
- 盒子不會產生換行。
- [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width)和[`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height)屬性將不起作用。
- 垂直方向的內邊距(`padding`)、外邊距(`margin`)以及邊框(`border`)會被套用，但是不會把其他處於`inline`狀態的盒子推開。
- 水平方向的內邊距(`padding`)、外邊距(`margin`)以及邊框(`border`)會被套用，且會把其他處於`inline`狀態的盒子推開。

某些HTML 元素，如`<a>`、`<span>`、`<em>`以及`<strong>`，預設使用`inline`作為外部顯示類型。