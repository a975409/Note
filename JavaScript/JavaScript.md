
參考資料：
[JavaScript 參考文件](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference)
[專為中學生寫的 JavaScript 程式書](https://ccckmit.gitbooks.io/javascript/content/)
[fooish Javascript教學](https://www.fooish.com/javascript/)
[網址編碼 encodeURI vs encodeURIComponent](https://kamadiam.com/encodeuri-vs-encodeuricomponent/#google_vignette)
[JavaScript 常用編碼、解碼](https://www.cythilya.tw/2020/07/24/encode-decode/)
[Fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/fetch#%E7%A4%BA%E4%BE%8B)
[如何用 JavaScript 讀取 Cookie](https://www.shubo.io/cookies/#%E5%A6%82%E4%BD%95%E7%94%A8-javascript-%E8%AE%80%E5%8F%96-cookie)
[解決 Vue Js 使用 jQuery DatePicker 時，抓不到日期 value 問題](https://dotblogs.com.tw/marsxie/2017/07/19/190143)
[Using custom elements - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_custom_elements#examples)
[圖片懶加載Lazy Loading範例應用](https://ithelp.ithome.com.tw/articles/10332085)

個人筆記：
[[function]]
[[object物件]]
[[(ES6)Class繼承]]
[[原型繼承]]
[[輕鬆理解JS 原型、原型鏈]]
[[this 是指當下的執行者是誰]]
[[window 瀏覽器操作]]
[[Document DOM操作]]
[[非同步 Promise]]
[[AJAX - XMLHttpRequest]]
[[AJAX - XMLHttpRequest 事件]]
[[AJAX - XMLHttpRequest 上傳檔案]]
[[在瀏覽器中使用JavaScript module(模組)]]
[[存取 HTMLElement的資料集屬性(data-)]]
[[自定義元素( Web Component )]]
[[ES6模組（module）]]
[[嚴格模式]]

e.preventDefault()：觸發事件時，停止觸發物件的預設操作，常用在"提交表單"的事件，例如：
```javascript
<form className="create-form" onSubmit={handleSubmit}>
      <input
        type="text"
        placeholder="輸入待辦事項"
        value={content}
        onChange={(e) => {
          setContent(e.target.value);
        }}
      ></input>

      <button type="submit">加入</button>

    </form>
```

e.stopPropagation()：阻止事件向上傳遞：
```javascript
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('你点击了 toolbar ！');
    }}>
      <Button onClick={() => alert('正在播放！')}>
        播放电影
      </Button>
      <Button onClick={() => alert('正在上传！')}>
        上传图片
      </Button>
    </div>
  );
}
```
# Array
1. 複製陣列：
```javascript
const squares = Array(9).fill(null);
const nextSquares = squares.slice();
nextSquares[i] = "X";
```

 2. 建立指定陣列元素數量的空陣列：
 ```javascript
 const squares = Array(9).fill(null);
 ```

3. 延展或合併陣列元素：
```javascript
//...history代表為展開陣列元素
[...history, nextSquares]
```

物件(object)展開語法：
```javascript
setPerson({
  ...person, // 复制上一个 person 中的所有字段
  firstName: e.target.value // 但是覆盖 firstName 字段 
});
```