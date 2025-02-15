[HttpHeaders](https://angular.tw/api/common/http/HttpHeaders)

```typescript

//引用HttpHeaders
import { HttpClient, HttpHeaders } from '@angular/common/http';

import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { BehaviorSubject, Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { environment } from '../environments/environment';
import { TokenService } from './token.service';

@Injectable({
  providedIn: 'root',
})
export class AuthService {
  private currentUserSubject = new BehaviorSubject<any>(null);
  public currentUser = this.currentUserSubject.asObservable();

  constructor(private http: HttpClient, private router: Router,private tokenService:TokenService) {}

  //建立HttpHeader
  httpOptions = {
    headers: new HttpHeaders({
      'request-device': 'web',
    })
  };

  login(username: string, password: string): Observable<any> {
    //呼叫HttpPost，帶入HttpHeader
    return this.http
      .post(`${environment.apiUrl}/Auth/login`, { username, password },this.httpOptions)
      .pipe(
        map((response: any) => {
          const user = { username: response.username, role: response.role };
          this.currentUserSubject.next(user);
          this.router.navigate([user.role === 'admin' ? '/admin' : '/user']);
          return user;
        })
      );
  }
}
```
