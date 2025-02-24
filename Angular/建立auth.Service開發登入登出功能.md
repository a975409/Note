
```typescript
import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { BehaviorSubject } from 'rxjs';
import { ApiService } from './api.service';
import { LoginContract } from './ApiContracts/login-contract';
import { HttpOptionService } from './http-option.service';
import { User } from './Models/user';
import { UserType } from './Models/user-type';

@Injectable({
  providedIn: 'root',
})
export class AuthService {
  private gusetUser: User = {
    apiCode: '',
    apiToken: '',
    userType: UserType.訪客,
    isAdmin: false,
  };

  //記錄已登入的使用者資訊
  subUser$ = new BehaviorSubject<User>(this.gusetUser);
  
  apitoken: string = '';
  apiCode: string = '';
  httpOptions = {};
  constructor(
    private router: Router,
    private apiService: ApiService,
    private httpOptionService: HttpOptionService
  ) {}

  /**登入 */
  login(username: string, password: string): void {
    let newUser: User;

    this.apiService
      .httpPost(
        'Auth/login',
        {
          loginId: username,
          passwd: password,
        },
        '登入失敗，請稍後再試'
      )
      .subscribe({
        next: (result: LoginContract) => {
          if (result.status == 1) {
            newUser = {
              apiCode: result.code,
              apiToken: result.token,
              userType: result.type,
              isAdmin: result.IsAdmin == 1,
            };

			//登入成功後記錄使用者資訊
            this.subUser$.next(newUser);
            if (newUser.userType == UserType.司機) {
              this.router.navigate(['/driver']);
            }
          } else {
          }
        },
        error: (err) => {
          console.log(err);
        },
      });
  }

  /**登出 */
  logout() {
    this.apiService.httpGet('Auth/logout').subscribe((result) => {
			
	  //登出後清除使用者資訊
      this.subUser$.next(this.gusetUser);
      this.router.navigate(['/login']);
    });
  }

  /**取得目前已登入的使用者資訊 */
  getCurrentUserValue() {
    return this.subUser$.value;
  }
}
```

