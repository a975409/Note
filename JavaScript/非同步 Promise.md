[Promise](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise)

```javascript
function asyncProcess(value){
	return new Promise((resolve,reject)=>{
		if(value){
			//回傳執行成功的結果
			resolve(`輸入值：${value}`)
		}else{
			//回傳執行失敗的結果
			reject(`輸入為空值`)
		}
	})
}

//then接收Promise的執行結果
asyncProcess('TOKUZIROU').then(
	//success：接收由resolve函數回傳的結果
	response=>{
		console.log(response)
	},

	//failure：接收由reject函數回傳的結果
	error=>{
		console.log(`:${error}`)
	}
)
```

串聯多個`Promise`依序處理時，需在`success`回傳`Promise`物件：
```javascript
asyncProcess('TOKUZIROU')
.then(
	//success：接收由resolve函數回傳的結果
	response=>{
		console.log(response)

		//回傳下一個`Promise`物件
		return asyncProcess('NINNTOKUZIROU')
	},

	//failure：接收由reject函數回傳的結果
	error=>{
		console.log(`:${error}`)
	}
)
.then(
	//success：接收由resolve函數回傳的結果
	response=>{
		console.log(response)
	},

	//failure：接收由reject函數回傳的結果
	error=>{
		console.log(`:${error}`)
	}
)
```

判斷所有`Promise`物件執行成功時，`all`：
```javascript
Promise.all([
asyncProcess('TOKUZIROU'),
asyncProcess('NINNTOKUZIROU'),
asyncProcess('AAANINNTOKUZIROU')])
.then(
	//success：接收所有Promise物件的resolve函數回傳的結果，以陣列顯示
	response=>{
		console.log(response)
	},

	//failure：接收所有Promise物件的reject函數回傳的結果，以陣列顯示
	error=>{
		console.log(`:${error}`)
	}
)
```

判斷多個`Promise`物件，其中一個執行成功時，`race`：
```javascript
Promise.race([
asyncProcess('TOKUZIROU'),
asyncProcess('NINNTOKUZIROU'),
asyncProcess('AAANINNTOKUZIROU')])
.then(
	//success：接收所有Promise物件的resolve函數回傳的結果，以陣列顯示
	response=>{
		console.log(response)
	},

	//failure：接收所有Promise物件的reject函數回傳的結果，以陣列顯示
	error=>{
		console.log(`:${error}`)
	}
)
```