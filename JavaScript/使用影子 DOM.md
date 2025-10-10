[使用影子 DOM](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_shadow_DOM)

建立一個影子DOM：
```html
<div id="host"></div>
<span>I'm not in the shadow DOM</span>
<script>
	const host = document.querySelector("#host");
	
	<!--在#host下建立一個影子DOM -->
	const shadow = host.attachShadow({ mode: "open" });
	
	<!--建立一個span元素 -->
	const span = document.createElement("span");
	span.textContent = "I'm in the shadow DOM";
	
	<!--在影子DOM內添加一個 span -->
	shadow.appendChild(span);
</script>
```

`javascript`只能透過`shadowRoot`屬性存取影子DOM：
```html
<div id="host"></div>
<span>I'm not in the shadow DOM</span>
<br />

<button id="upper" type="button">将 span 元素转换为大写</button>

<script>
	const host = document.querySelector("#host");
	
	<!--在#host下建立一個影子DOM -->
	<!--影子DOM需設為{ mode: "open" }才可透過`shadowRoot`屬性存取 -->
	<!--影子DOM設為{ mode: "closed" }就無法透過`shadowRoot`屬性存取 -->
	const shadow = host.attachShadow({ mode: "open" });
	
	<!--建立一個span元素 -->
	const span = document.createElement("span");
	span.textContent = "I'm in the shadow DOM";
	
	<!--在影子DOM內添加一個 span -->
	shadow.appendChild(span);

	<!--指定#upper 按鈕 -->
	const upper = document.querySelector("button#upper");
	upper.addEventListener("click", () => {
	  
	  <!--只能透過`shadowRoot`屬性存取影子DOM -->
	  const spans = Array.from(host.shadowRoot.querySelectorAll("span"));
	  for (const span of spans) {
	    span.textContent = span.textContent.toUpperCase();
	  }
	});
</script>
```

影子DOM只能透過以下方式設定`css`樣式：
```html
<div id="host"></div>
<span>I'm not in the shadow DOM</span>
<br />

<button id="upper" type="button">将 span 元素转换为大写</button>

<script>
	const host = document.querySelector("#host");
	
	<!--在#host下建立一個影子DOM -->
	const shadow = host.attachShadow({ mode: "open" });
	
	const sheet = new CSSStyleSheet();
	sheet.replaceSync("span { color: red; border: 2px dotted black;}");
	
	<!--影子DOM設定樣式 -->
	shadow.adoptedStyleSheets = [sheet];
	
	const span = document.createElement("span");
	span.textContent = "I'm in the shadow DOM";
	shadow.appendChild(span);
</script>
```

[影子 DOM 和自定义元素](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_components/Using_shadow_DOM#%E5%BD%B1%E5%AD%90_dom_%E5%92%8C%E8%87%AA%E5%AE%9A%E4%B9%89%E5%85%83%E7%B4%A0)