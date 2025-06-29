[摸熟 This 的運作](https://www.casper.tw/development/2020/09/28/this-homework/#google_vignette)
[JavaScript 的 this 到底是誰？](https://www.casper.tw/javascript/2017/12/12/javascript-this/)

1. 建立一個物件樣板如下：
```javascript
class Card{
	constructor(initName){
		this.name=initName;
	}

	//定義function
	hello(){
		console.log('hello',this.name);
	}
}
```

等同於
```javascript
function Card(initName){
	this.name=initName;
}

//定義function
Card.prototype.hello=function(){
	console.log('hello',this.name);
}
```

> `function`是定義在物件的`prototype`內，所以當建立100個物件時，也只會共用一個`function`

2. 當設定為`a.helloooo=c1.hello`，然後分別呼叫之後，會輸出不一樣的結果：
```javascript
const c1=new Card('布魯斯')
c1.hello()

const a={name:'AA'}
a.helloooo=c1.hello
a.helloooo()

//執行結果
//hello,布魯斯
//hello,AA
```

>原因在於`a.helloooo=c1.hello`這段語法就只是在`a.prototype`設定`helloooo`這個`function`而已，`this`則是代表執行者本身，所以`this.name`才會有不一樣的結果

3. 如果要在物件內直接建立`function`，而不是建立在`prototype`內的話，有以下做法
第一種
```javascript
class Card{
	constructor(initName){
		this.name=initName;
		
		//綁定this
		this.hello=this.hello.bind(this);
	}

	//定義function
	hello(){
		console.log('hello',this.name);
	}
}
```

第二種
```javascript
class Card{
	constructor(initName){
		this.name=initName;
	}

	//定義function
	hello = () =>{
		console.log('hello',this.name);
	}
}
```

執行結果：
```javascript
const c1=new Card('布魯斯')
c1.hello()

const a={name:'AA'}
a.helloooo=c1.hello
a.helloooo()

//執行結果
//hello,布魯斯
//hello,布魯斯
```