[Web Component 學習筆記 | Johnny Wang Blog](https://johnnywang1994.github.io/book/articles/js/web-component.html)
[Web Component](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components)
[使用自定义元素](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_custom_elements)
[獨立自訂元素範例](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_custom_elements#%E4%B8%80%E4%B8%AA%E7%8B%AC%E7%AB%8B%E8%87%AA%E5%AE%9A%E4%B9%89%E5%85%83%E7%B4%A0)
[自訂內建元素範例](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_custom_elements#%E8%87%AA%E5%AE%9A%E4%B9%89%E5%86%85%E7%BD%AE%E5%85%83%E7%B4%A0)

### Web Component 的其他應用方式：
[[使用影子 DOM]]
[[使用模板(template)和插槽(Slot)]]

### 基礎實作如下：
```javascript
class WordCount extends HTMLElement {
  constructor() {
    super();
  }
  // 此处编写元素功能
}
```

### 完整自訂義元素和使用方式：

1. 自訂義元素生命週期事件：
```javascript
// 为这个元素创建类
class MyCustomElement extends HTMLElement {
   //指定要監測的屬性，可設定多個
  static observedAttributes = ["color", "size"];

  constructor() {
    // 必须首先调用 super 方法
    super();
  }

  connectedCallback() {
  
    //每當元素加入到文件中時調用。建議在此事件中實作自訂元素的設定，而不是在建構函式中實作。
    
    // 创建影子根
    const shadow = this.attachShadow({ mode: "open" });

    // 创建几个 span
    const wrapper = document.createElement("span");
    wrapper.setAttribute("class", "wrapper");

    // 插入图标
    let imgUrl;
    if (this.hasAttribute("img")) {
      imgUrl = this.getAttribute("img");
    } else {
      imgUrl = "img/default.png";
    }

    const img = document.createElement("img");
    img.src = imgUrl;

    // 创建一些 CSS 应用于影子 DOM
    const style = document.createElement("style");
    console.log(style.isConnected);

    style.textContent = `
      .wrapper {
        position: relative;
      }

      img {
        width: 1.2rem;
      }
    `;

    // 創建的HTTML元素，最終需附加到影子 DOM 上，這樣引用此自訂元素時才會出現
    shadow.appendChild(style);
    console.log(style.isConnected);
    shadow.appendChild(wrapper);
  }

  disconnectedCallback() {
  
    //每當元素從文檔中移除時調用。
    console.log("自定义元素从页面中移除。");
  }

  adoptedCallback() {
  
	//每當元素被移動到新文檔時調用。
    console.log("自定义元素移动至新页面。");
  }

  //name: 發生變化的屬性的名稱。
  //oldValue: 屬性的舊值。
  //newValue: 屬性的新值。
  attributeChangedCallback(name, oldValue, newValue) {
  
	//在屬性變更、新增、移除或替換時呼叫
        console.log(`属性 ${name} 已由 ${oldValue} 变更为 ${newValue}。`);
  }
}

customElements.define("my-custom-element", MyCustomElement);
```

- `attributeChangedCallback()`：在屬性變更、新增、移除或替換時呼叫。有關此回調的更多詳細信息，請參見[響應屬性變化](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_custom_elements#%E5%93%8D%E5%BA%94%E5%B1%9E%E6%80%A7%E5%8F%98%E5%8C%96)。

2. 註冊自訂元素：

> 若要使自訂元素在頁面中可用，請呼叫[`Window.customElements`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/customElements)的[`define()`](https://developer.mozilla.org/zh-CN/docs/Web/API/CustomElementRegistry/define "define()")方法。

```javascript
//註冊為獨立自訂元素
customElements.define("my-Custom", MyCustomElement);

//註冊為自訂內建元素，{ extends: "p" } 是指要擴充的 p 元素
customElements.define("my-Custom", MyCustomElement, { extends: "p" });
```

`define()`方法接受以下參數：

[`name`](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_custom_elements#name)
HTML 元素的名稱。`必須以小寫字母開頭，並包含一個連字符`，並符合規範中有效名稱的定義中列出的一些其他規則。

[`constructor`](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_custom_elements#constructor)
自訂元素的建構函數。

[`options`](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_custom_elements#options)
僅對於自訂內建元素，這是一個包含單一屬性`extends`的對象，該屬性是一個字串，命名了要擴展的內建元素。

3. 使用自訂元素
```html
<!-- 獨立自訂元素 -->
<my-Custom>
  <!-- 元素的内容 -->
</my-Custom>

<!-- 自訂內建元素，需用 is 指定 name 名稱 -->
<p is="my-Custom"></p>
```

### this 指向創建的 dom 節點
```javascript
// 創建 Custom Element
class MyButton extends HTMLElement {
  constructor() {
    // 一定要先繼承
    super();
  }
  
  connectedCallback() {
	// this 指向創建的 dom 節點
    this.innerHTML = '<button>按鈕</button>';
  }
}

customElements.define('my-button', MyButton);
```