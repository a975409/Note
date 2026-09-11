[[上傳單一檔案]]
[[上傳多個檔案(.NET 8)]]
[[上傳大型檔案(需用MultipartReader)]]
[[檔案驗證標籤屬性UploadFileValid]]

`IFormFileCollection` 不需要加任何 binding attribute，ASP.NET Core 會自動從 multipart/form-data 中依**參數名稱**對應抓取檔案

可加上[Consumes("multipart/form-data")]標記，宣告接收 Form 表單

### 前端顯示進度條(後端無須調整)

1. 單一檔案：
```javascript
const xhr = new XMLHttpRequest();
xhr.open('POST', '/Home/UploadLarge', true);

xhr.upload.onprogress = function (event) {
  if (event.lengthComputable) {
    const percent = (event.loaded / event.total) * 100;
    console.log(`上傳進度: ${percent.toFixed(2)}%`);
    // 更新畫面上的 progress bar
  }
};

xhr.onload = function () {
  if (xhr.status === 200) {
    console.log('上傳成功');
  }
};

const formData = new FormData();
formData.append('file', fileInput.files[0]);
xhr.send(formData);
```

2. 多檔案放同一個 Request，只有「整體進度條」：
```javascript
const formData = new FormData();
const files = fileInput.files; // FileList，可能有多個檔案

for (let i = 0; i < files.length; i++) {
  formData.append('files', files[i]); // 全部塞進同一個 formData
}

const xhr = new XMLHttpRequest();
xhr.open('POST', '/Home/UploadLarge', true);

xhr.upload.onprogress = function (event) {
  if (event.lengthComputable) {
    const percent = (event.loaded / event.total) * 100;
    console.log(`整體上傳進度: ${percent.toFixed(2)}%`);
  }
};

xhr.onload = () => console.log('全部上傳完成');
xhr.send(formData);
```

>**特性**：只有一條進度條，代表「這批檔案全部加總的位元組」上傳到哪，**無法得知個別檔案各自完成多少**。

3. 每個檔案各自送一個 Request，可以做「每個檔案獨立進度條」
```javascript
function uploadFile(file, onProgress) {
  return new Promise((resolve, reject) => {
    const formData = new FormData();
    formData.append('file', file);

    const xhr = new XMLHttpRequest();
    xhr.open('POST', '/Home/UploadLarge', true);

    xhr.upload.onprogress = (event) => {
      if (event.lengthComputable) {
        const percent = (event.loaded / event.total) * 100;
        onProgress(percent);
      }
    };

    xhr.onload = () => xhr.status === 200 ? resolve() : reject(xhr.statusText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send(formData);
  });
}

// 使用範例：多檔案，各自獨立顯示進度
async function uploadAll(files) {
  for (let i = 0; i < files.length; i++) {
    await uploadFile(files[i], (percent) => {
      console.log(`檔案 ${files[i].name} 進度: ${percent.toFixed(2)}%`);
      // 更新對應這個檔案的 UI 進度條
    });
  }
}
```