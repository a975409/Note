
需引用如下：
```typescript
import { Observable, throwError } from 'rxjs';
import { catchError, map } from 'rxjs/operators';
```

#### 以http.get為例(沒有處理例外錯誤)：
```typescript
//this.configService.getConfig()
configUrl = 'assets/config.json';

getConfig() {
  return this.http.get<Config>(this.configUrl);
}

//呼叫端
showConfig() {
  this.configService.getConfig()
    .subscribe((data: Config) => this.config = {
        heroesUrl: data.heroesUrl,
        textfile:  data.textfile,
        date: data.date,
    });
}
```

> 建立`Observable`類後，需呼叫`.subscribe()`，這個`Observable`類才會執行；並且在`.subscribe()`內抓取接收到的資料
#### 以http.get為例(有處理例外錯誤)：
```typescript
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { environment } from '../environments/environment';
import { Observable, throwError } from 'rxjs';
import { catchError, map } from 'rxjs/operators';

@Injectable({
  providedIn: 'root',
})
export class ApiService {
  constructor(private http: HttpClient) {}

  getDomainAPIUrl(): Observable<string> {
    return this.http.get<string>(`${environment.versionAPI}`).pipe(
      //先在pipe內定義執行成功後要回傳什麼資料，透過map定義
      map((version: string) => `${environment.apiUrl}/${version}`),
      
      //先在pipe內定義執行失敗後要回傳什麼錯誤，透過catchError定義
      catchError(error => {
        console.error('API 請求錯誤:', error);
        return throwError(() => new Error('無法取得 API 版本，請稍後再試。'));
      })
    );
  }
}
```

如何呼叫：
```typescript
this.apiService.getDomainAPIUrl().subscribe({
  next: apiUrl => {
    console.log('API URL:', apiUrl);
  },
  error: err => {
    console.error('發生錯誤:', err.message);
  }
});
```

> 建立`Observable`類後，需呼叫`.subscribe()`，這個`Observable`類才會執行；並且在`.subscribe()`內可設定`next`接收回傳的參數，以及`error`接收錯誤訊息

