
Box有以下屬性，以下圖所示：
![[Pasted image 20250707105454.png]]

- `padding`（內邊距）：是指內容周圍的空間。在下面的例子中，它是段落文字周圍的空間。
- `border`（邊框）：是緊接著內邊距的實線。
- `margin`（外邊距）：是圍繞元素邊框外側的空間。

`box`置中：
> `display: block`img為[[行內盒子(inline boxes)]]，所以需設為[[區塊盒子(block boxes)]]，才可以使用margin屬性
```CSS
img {
  display: block;
  margin: 0 auto;
}
```

`box`外部顯示類型：以[display](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display)設定`box`樣式，影響自身的大小顯示，以及與其他`box`的編排方式
[[區塊盒子(block boxes)]]
[[行內盒子(inline boxes)]]

`box`內部顯示類型：以