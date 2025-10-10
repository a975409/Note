[使用模板和插槽](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_templates_and_slots)

### 模板(template)

定義模板：
```html
<template id="my-paragraph">
  <style>
    p {
      color: white;
      background-color: #666;
      padding: 5px;
    }
  </style>
  <p>我的段落</p>
</template>
```

使用 JavaScript 取得模板並附加到 `body` 中：
```javascript
let template = document.getElementById("my-paragraph");
let templateContent = template.content;
document.body.appendChild(templateContent);
```

### 在[[自定義元素( Web Component )]]使用模板(固定寫法)：
```javascript
customElements.define(
  "my-paragraph",
  class extends HTMLElement {
    constructor() {
      super();
      
      //取得模板
      let template = document.getElementById("my-paragraph");
      let templateContent = template.content;

	  //建立影子DOM
      const shadowRoot = this.attachShadow({ mode: "open" });
      shadowRoot.appendChild(templateContent.cloneNode(true));
    }
  },
);
```

> 透過[`Node.cloneNode()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/cloneNode)方法將模版複製一個出來，然後添加到影子DOM下
> 另外因為有在模板內設定css樣式，所以添加到影子DOM後也會套用其樣式

使用[[自定義元素( Web Component )]]
```html
<my-paragraph></my-paragraph>
```

### 插槽(Slot)

> `<slot>` 元素只能在 Shadow DOM 中使用才有插槽效果，否则，就可以看成是个普通的 HTML 元素。

在模板添加插槽(Slot)：
```html
<template id="my-paragraph">
	<p>
		<!-- 添加插槽(Slot)  -->
		<slot name="my-text">我的默认文本</slot>
	</p>
</template>
```

> `slot name`是模板的唯一值，不能重複

在[[自定義元素( Web Component )]]使用模板：
```javascript
customElements.define(
  "my-paragraph",
  class extends HTMLElement {
    constructor() {
      super();
      
      //取得模板
      let template = document.getElementById("my-paragraph");
      let templateContent = template.content;

	  //建立影子DOM
      const shadowRoot = this.attachShadow({ mode: "open" });
      shadowRoot.appendChild(templateContent.cloneNode(true));
    }
  },
);
```

使用[[自定義元素( Web Component )]]後使用插槽(Slot)：
```html
<my-paragraph>
  <span slot="my-text">让我们使用一些不同的文本！</span>
</my-paragraph>
```