
## 通過 CDN 使用 Vue

>通過 CDN 使用 Vue 時，不涉及“構建步驟”。這使得設置更加簡單，並且可以用於增強靜態的 HTML 或與後端框架集成。但是，你將無法使用`單文件組件 (SFC)`語法。

### 使用 ES 模塊構建版本

先使用[導入映射表 (Import Maps)](https://caniuse.com/import-maps) 告訴瀏覽器如何導入`vue`：

```html
<script type="importmap"> 
{ "imports": { "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js" } } 
</script>
```

```html
<div id="app">
  {{ count }}
  <button v-on:click="increment">按我</button>
</div>

<script type="module">
  import { createApp, ref } from 'vue'

  const app = createApp({
	setup() {
		// 變數，需設定為響應式狀態
		const count = ref(0);
		
		// 定義函數
		function increment() {
			count.value++;
		}

		// 生命週期鉤子
		onMounted() {
		  console.log(`The initial count is ${count.value}.`);
		},	
	
		//開放外部存取的變數和函數
		return {
			count,
			increment,
		};
	},
  });
  
  app.mount("#app");
</script>
```

 `ES 模塊構建版本` vs `全局構建版本`引用`vue`方式：
```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp, ref } = Vue;
  
  const app = createApp({
	setup() {
		// 變數，需設定為響應式狀態
		const count = ref(0);
		
		// 定義函數
		function increment() {
			count.value++;
		}

		// 生命週期鉤子
		onMounted() {
		  console.log(`The initial count is ${count.value}.`);
		},	
	
		//開放外部存取的變數和函數
		return {
			count,
			increment,
		};
	},
  });
  
  app.mount("#app");
</script>
```

### 拆分模塊

> 將代碼分割成單獨的 JavaScript 文件，以便更容易管理。例如：

```html
<!-- index.html -->
<div id="app"></div>

<script type="module">
  import { createApp } from 'vue'
  import MyComponent from './my-component.js'

  createApp(MyComponent).mount('#app')
</script>
```

```javascript
// my-component.js
import { ref } from 'vue'
export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `<div>Count is: {{ count }}</div>`
}
```

## 創建一個 Vue 應用

> 生成的項目中的示例組件使用的是[組合式 API](https://zh-hk.vuejs.org/guide/introduction.html#composition-api) 和 `<script setup>`，而非[選項式 API](https://zh-hk.vuejs.org/guide/introduction.html#options-api)

### 單文件組件

> 也被稱為 `*.vue` 文件，英文 Single-File Components，縮寫為 **SFC**，通常應用於 Vue [單頁應用](https://zh-hk.vuejs.org/guide/extras/ways-of-using-vue.html#single-page-application-spa)
> 如果是採用CDN引用Vue套件的方式，是無法使用`單文件組件`的，因為需要執行編譯

```vue
<script setup>
	import { ref } from 'vue'
	const count = ref(0)
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
	button {
	  font-weight: bold;
	}
</style>
```

> `<script setup>`僅適用於`單文件組件`

