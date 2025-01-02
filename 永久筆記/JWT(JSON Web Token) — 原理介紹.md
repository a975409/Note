
# JWT的組成

JWT的組合可以看成是三個JSON object，並且用 . 來做區隔，而這三個部分會各自進行編碼，組成一個JWT字串。

也就是變成：xxxxx.yyyyy.zzzzz，分別是Header、Payload、Signature

### Header：
1. alg：也就是token被加密的演算法，如**HMAC**、**SHA256**、**RSA**

2. typ：也就是token的type，基本上就是**JWT**

範例：
```JSON
{  
    "alg": "HS256",  
    "typ": "JWT"  
}
```

### Payload：
這裡放的是聲明(Claim)內容，也就是用來放傳遞訊息的地方，在定義上有三種聲明：
1. Registered claims

可以想成是標準公認的一些訊息**建議**你可以放，但並不強迫，例如：

- iss(Issuer)：JWT簽發者
- exp(Expiration Time)：JWT的過期時間，過期時間必須大於簽發JWT時間
- sub(Subject)：JWT所面向的用戶
- aud(Audience)：接收JWT的一方
- nbf(Not Before)：也就是定義擬發放JWT之後，的某段時間點前該JWT仍舊是不可用的
- iat(Issued At)：JWT簽發時間
- jti(JWT Id)：JWT的身分標示，每個JWT的Id都應該是不重複的，避免重複發放

2. Public claims

這個，可以想成是傳遞的欄位必須是跟上面Registered claims欄位不能衝突，然後可以向官方申請定義公開聲明，會進行審核等步驟，實務上在開發上是不太會用這部分的。

3. Private claims

這個就是發放JWT伺服器可以自定義的欄位的部分，例如實務上會放User Account、User Name、User Role等**不敏感**的數據。

所謂不敏感的數據就是不會放使用者的密碼等敏感數據，因為該Payload傳遞的訊息最後也是透過Base64進行編碼，所以是可以被破解的，因此放使用者密碼會有安全性的問題。

範例：
```JSON
{  
  "sub": "1234567890",  
  "account": "[kenny@example.com](mailto:kenny@example.com)",  
  "role": "admin"  
}
```

### Signature：
由三大部分組成：

```JSON
base64UrlEncode(header)
base64UrlEncode(payload)
secret
```

也就是：

```JSON
HMACSHA256(  
  base64UrlEncode(header) + "." +  
  base64UrlEncode(payload),  
  secret)
```

>secret是存放在伺服器端的秘密字串，這個secret一旦外洩給客戶端，客戶端就可以自己產生JWT，並且透過該JWT存取資源，因此secret是永遠不該外流的

[參考資料](https://medium.com/%E4%BC%81%E9%B5%9D%E4%B9%9F%E6%87%82%E7%A8%8B%E5%BC%8F%E8%A8%AD%E8%A8%88/jwt-json-web-token-%E5%8E%9F%E7%90%86%E4%BB%8B%E7%B4%B9-74abfafad7ba)
