預設CSS樣式如下：
```CSS
div {
  display: block;
}
```

有以下特性
- 盒子後面會接一個換行。
- [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width)和[`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height)屬性可以發揮作用，但只針對內容寬(width)高(hight)做設定，不包含`padding`、`border`、`margin`設定的邊框長度。
- 內邊距、外邊距和邊框會將其他元素從目前盒子周圍「推開」。
- 如果未指定[`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width)，則方框將沿行向擴展，以填充其容器中的可用空間。在大多數情況下，盒子會變得與其容器一樣寬，佔據可用空間的100%。

某些 HTML 元素，例如`<h1>`和`<p>`，預設用作`block`外部顯示類型。