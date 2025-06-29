![[Pasted image 20250629220134.png]]

1. js分為**函數對**象和**普通物件**，每個物件都有__proto__屬性，但是只有函數物件才有prototype屬性
2. Object、Function都是js內建的**函數**, 類似的還有我們常用到的Array、RegExp、Date、Boolean、Number、String

以下列程式碼為例(function Foo())：
```javascript
//建立Foo類別
function Foo()

//建立 f1、f2 的物件
let f1 = new Foo();
let f2 = new Foo();
```

1. 當建立Foo類別(原型 (prototype))時，會建立對應的prototype
2. 物件 f1、f2 的建立，是透過__proto__屬性指向的prototype

承以上2點說明，簡單來說就是 Foo類別建立後，會建立對應的prototype 建立f1、f2 物件後，透過__proto__屬性可知道該物件建立時所參考的prototype：
```javascript
f1.__proto__ === Foo.prototype;
f2.__proto__ === Foo.prototype;
```

JavaScript與C# 建立物件的做法差異點如下：
1. C#：建立物件時直接指定參考的類別(Class)
2. JavaScript：建立物件時不是直接指定參考的類別(原型 (prototype))，而是指定prototype的方式建立物件；也就是說物件與類別兩者之間並不是直接關聯，而是直接參考prototype，這做法就類似於透過Instances實現介面隔離原則的方式，所以才能夠在定義好類別後，透過prototype隨意新增屬性或方法

## 定義類別的屬性 (Property) - _this_
```javascript
var Person = function (nickname) {
    this.nickname = nickname;
};
```
## 定義類別的方法 (Method) - _prototype_

```javascript
Person.prototype.sayHello = function() {
    alert('Hello, I\\\\'m ' + this.nickname);
};
```

透過this的方式定義屬性、以及透過prototype定義方法這兩者差異點如下：
1. 建立物件後，每個物件都會有獨立的屬性，也就是說每個物件的屬性值可以不同
2. 建立物件後，每個物件都共用同一個方法

參考網址： [轻松理解JS 原型原型链 - 掘金 (juejin.cn)](https://juejin.cn/post/6844903989088092174)[https://youtu.be/1pYtVwIAvhY?t=7747](https://youtu.be/1pYtVwIAvhY?t=7747)