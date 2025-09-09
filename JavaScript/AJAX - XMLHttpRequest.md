[XMLHttpRequest](https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest)

範例如下：
```html
<div id="result"></div>
```

```javascript
var result=document.getElementById('result')

//建立 XMLHttpRequest 物件
var xhr=new XMLHttpRequest();

//定義 onreadystatechange 事件
xhr.onreadystatechange=function(){
	//通訊結束
	if(xhr.readyState===4){
		//通訊成功，HttpStatusCode=200
		if(xhr.status===200){
			result.textConent=xhr.responseText
		}else{
			result.textConent='伺服器發生錯誤'
		}
	}else{
		result.textConent='通訊中...'
	}
}

//開啟連線
xhr.open('GET','api網址',true)

//呼叫API
xhr.send(null)
```

> api網址所附帶的參數，需經由`encodeURIComponent`編碼處理

[XMLHttpRequest.open](https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest/open#%E8%AA%9E%E6%B3%95)
[XMLHttpRequest.send](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/send)
[XMLHttpRequest.readyState](https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest/readyState)
[HttpStatusCode](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Reference/Status)

上述範例可用監聽事件改寫：
```javascript
var result=document.getElementById('result')

//建立 XMLHttpRequest 物件
var xhr=new XMLHttpRequest();

xhr.addEventListener('loadstart',function(){
	result.textConent='通訊中...'
},false);

xhr.addEventListener('load',function(){
	result.textConent=xhr.responseText
},false);

xhr.addEventListener('error',function(){
	result.textConent='伺服器發生錯誤'
},false);

//開啟連線
xhr.open('GET','api網址',true)

//呼叫API
xhr.send(null)
```



`Content-type`設定方式如下：
```javascript
xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded;charset=UTF-8");
```

傳送資料常用`Content-type`：
- application/json(Request Body)： JSON資料格式
- multipart/form-data(Request Form) ： 需要在表单中进行文件上传时，就需要使用该格式
- application/x-www-form-urlencoded(Request Form) ： `<form encType=””>`中默认的encType，form表单数据被编码为key/value格式发送到服务器（表单默认的提交数据的格式）
- 其他常用[HTTP content-type](https://www.runoob.com/http/http-content-type.html)



