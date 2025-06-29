```javascript
// 假設有一個 Blob 物件，type 為 text/plain，內容為 "hello world"
const myBlob = new Blob(['hello world'], { type: 'text/plain' });

// 上傳這個 Blob
uploadBlob(myBlob, 'hello.txt');

function uploadBlob(blob, filename) {
  const formData = new FormData();

  //**將Blob 物件添加到FormData:**
  formData.append('file', blob, filename);

  const xhr = new XMLHttpRequest();
  xhr.open('POST', '/upload', true);
  xhr.setRequestHeader("Content-type", "multipart/form-data;charset=UTF-8");

  xhr.onload = function() {
    if (xhr.status >= 200 && xhr.status < 300) {
      console.log('成功上傳', xhr.responseText);
    } else {
      console.error('上傳失敗', xhr.status, xhr.statusText);
    }
  };

  xhr.onerror = function() {
    console.error('網絡錯誤');
  };

  xhr.send(formData);
}
```

>如果 `Content-Type` 標頭在發送之前未設定，瀏覽器會自動將其設定為 `multipart/form-data`

[FormData](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData)
[Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)