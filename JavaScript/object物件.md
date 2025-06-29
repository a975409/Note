有以下2種建立物件的方式
1. 直接建立物件
```javascript
//一般建立物件的方式
const card={
  name:'小明'
}

//構造函數 function 建立物件樣板

```

2. 構造函數 function 建立物件樣板，之後就以new的方式建立物件就好：
```javascript
//建立物件樣板
function createCard(initName){
	this.name=initName;
}

//以new的方式建立物件
const a1=new createCard('小明')
const a2=new createCard('小明')
const a3=new createCard('小明')
```