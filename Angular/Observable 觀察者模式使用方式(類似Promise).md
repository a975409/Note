
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

> 如果有2個或以上的 Observable 會依序執行，則可將map替換為switchMap

switchMap應用情境：
```typescript
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable, throwError } from 'rxjs';
import { catchError, switchMap } from 'rxjs/operators';
import { environment } from '../../environments/environment';
import { HttpOptionService } from './http-option.service';

@Injectable({
  providedIn: 'root',
})
export class ApiService {
  constructor(
    private http: HttpClient,
    private httpOptionService: HttpOptionService
  ) {}

  httpOptions = {};

  httpGet(subUrl: string, errorMsg: string = ''): Observable<any> {
    
    return this.http
      .get(`${environment.versionAPI}`, { responseType: 'text' })
      .pipe(
        switchMap((version: string) => {
          this.httpOptions = this.httpOptionService.getCommonHttpOption();
          let apiUrl = `${environment.apiUrl}/${version}/${subUrl}`;

		  //執行另一個http.get，一樣回傳Observable
          return this.http.get<any>(apiUrl, this.httpOptions);
        }),
        catchError((error) => {
          console.error(error);
          return throwError(
            () => new Error(errorMsg || '發生錯誤，請稍後再試。')
          );
        })
      );
  }
}

```

> map、switchMap 取得值之後，可將取得的值做個轉換，到最後還是要將值傳遞下去(傳遞給下一個switchMap或tap)，tap則只有單純取得值而已

tap範例如下：
```typescript
private getApiVersion(): Observable<string> {
    if (this.apiVersion) {
      return of(this.apiVersion);
    } else {
      return this.http
        .get(${environment.versionAPI}, { responseType: 'text' })
        .pipe(
          map((version: string) => {
            this.apiVersion = version;
            return this.apiVersion;
          })
        );
    }

this.getApiVersion()
        .pipe(
          switchMap((version) => {
            this.httpOptions = this.httpOptionService.getCommonHttpOption();
            let apiUrl = `${environment.apiUrl}/${version}/${subUrl}`;
            return this.http.post<any>(
              apiUrl,
              my.clone({}, data),
              this.httpOptions
            );
          }),
          tap((response: DefaultResponseContract) => {
            if (response.unauth == 1) {
            }
          }),
          finalize(() => {
          })
        )
```

呼叫`subscribe`執行`Observable`：
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

`forkJoin`可一次性呼叫多個`Observable`，然後統一處理所有的執行結果：
```typescript
//設定批次呼叫多個API的執行方式
const apiCalls = submitData.map((item) => {
  return this.apiService
	.httpPost('ITT/Eways_ITTFEPickupData_AssignDriver', {
	  ITTNo: item.ITTNo,
	  DataList: item.DataList,
	})
	.pipe(
	  map((response: DefaultResponseContract) => {
		if (response.status == 1) {
		  return {
			status: 1,
			ITTNo: item.ITTNo,
			ErrorDescription: '',
		  };
		} else {
		  return {
			status: 0,
			ITTNo: item.ITTNo,
			ErrorDescription: response.ErrorDescription,
		  };
		}
	  })
	);
});

//呼叫subscribe才會批次呼叫API
if (apiCalls.length > 0) {
  forkJoin(apiCalls).subscribe({
	next: (results) => {

	  //取得每一個API，map回傳的執行狀態
	  const errors = results.filter(
		(result) => result && result.status != 1
	  );

	  if (errors.length > 0) {
		let errorITTNo = '';
		errors.forEach((error) => {
		  errorITTNo += error.ITTNo + '<br>';
		});

		// 有錯誤發生
		this.openAlert({
		  title: '錯誤',
		  message: `有以下ITT領櫃編號重新指派失敗，請檢查後重試：<br>${errorITTNo}`,
		});
	  } else {
		// 全部成功
		this.openAlert({
		  title: '成功',
		  message: '所有勾選的資料已成功重新指派司機',
		});
	  }

	  // 所有請求都已完成，可以處理結果
	  this.getResultdata();
	},
	error: (error) => {
	  // 處理錯誤情況
	  this.openAlert({
		title: '錯誤',
		message: '指派司機時發生錯誤',
	  });
	},
  });
}
```