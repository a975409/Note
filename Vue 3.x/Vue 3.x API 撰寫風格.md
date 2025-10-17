>以下是使用`ES 模塊構建版本`，可直接在HTML檔案內撰寫

### 選項式 API (Options API)

```html
<!--告訴瀏覽器如何導入`vue` -->
<script type="importmap"> 
{ "imports": { "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js" } } 
</script>

<div id="app">
  {{ count }}
  <button v-on:click="increment">按我</button>
</div>

<script type="module">
  import { createApp, ref } from 'vue'
  const app = createApp({
	// data() 返回的屬性將會成為響應式的狀態
	// 用來存變數
	// 並且暴露在 `this` 上
	data() {
	  return {
		count: 0,
	  };
	},

	// methods 是一些用來更改狀態與觸發更新的函數
	// 它們可以在模板中作為事件處理器綁定
	methods: {
	  increment() {
		this.count++;
	  },
	},

	// 生命週期鉤子會在組件生命週期的各個不同階段被調用
	// 例如這個函數就會在組件掛載完成後被調用
	mounted() {
	  console.log(`The initial count is ${this.count}.`);
	},
  });

  app.mount("#app");
</script>
```

### 組合式 API (Composition API)

```html
<!--告訴瀏覽器如何導入`vue` -->
<script type="importmap"> 
{ "imports": { "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js" } } 
</script>

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
		onMounted(()=>{
			console.log(`The initial count is ${count.value}.`);
		});
	
		//開放外部存取的變數和函數
		return {
			count,
			increment,
		};
	}
  });
  
  app.mount("#app");
</script>
```

> 存取`const count = ref(0);`變數方式為`count.value`