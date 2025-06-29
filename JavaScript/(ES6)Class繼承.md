
1. 以下兩者是相等的，都是建立物件樣板(原型)
```javascript
class Card{
 constructor(initName){
	 this.name=initName
 }
}
```

等同於
```javascript
function createCard(initName){
	this.name=initName;
}
```

2. 建立物件
```javascript
const c1=new Card('布魯斯')
```

3. 繼承`extends`，範例如下：
```javascript
class Car{
	constructor(initName){
		this.name=initName
	}
	start(){
		console.log('車子啟動')
	}
}

class Porshe extends Car{
	constructor(namePorshe){
		//將值傳遞至父class的建構子
		super(namePorshe)
	}
	start2(){
	    //呼叫父class的start();
		super.start();
		console.log('車子排氣');
	}

	//可複寫父class的start()
	start(){
		super.start();
		console.log('Porshe車子啟動');
	}
}

//實際應用
const p1=new Porshe('布魯斯的保時捷')
p1.start2();
p1.start();
```