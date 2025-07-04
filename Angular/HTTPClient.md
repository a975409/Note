[HttpClient](https://angular.tw/api/common/http/HttpClient)
[HTTP：從伺服器請求資料](https://angular.tw/guide/http-request-data-from-server#http-request-data-from-a-server)
[HTTP：向伺服器傳送資料](https://angular.tw/guide/http-send-data-to-server)

#### get()

>get預設回傳JSON格式的資料

1. 如果要回傳純文字的資料，則需加上` responseType: 'text' `的設定：
```typescript
this.http.get(`${environment.versionAPI}`, { responseType: 'text' })
```

設定其餘回傳格式，請參考此[連結](https://angular.tw/api/common/http/HttpClient#get)

