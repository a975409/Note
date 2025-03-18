
```typescript
import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { BehaviorSubject, Observable, of } from 'rxjs';
import { map, switchMap, tap } from 'rxjs/operators';
import { ReqLoginContract } from '../Contracts/RequestDto/Reqlogin-contract';
import { SignupContract } from '../Contracts/RequestDto/signup-contract';
import { CreateVerifyCodeResponse } from '../Contracts/ResponseDto/create-verify-code-response';
import { DefaultResponseContract } from '../Contracts/ResponseDto/default-response-contract';
import { DeviceCodeResponse } from '../Contracts/ResponseDto/device-code-response';
import { LoginContract } from '../Contracts/ResponseDto/login-contract';
import { Resend2FAResponse } from '../Contracts/ResponseDto/resend2-faresponse';
import { VerifyAdminCodeResponse } from '../Contracts/ResponseDto/verify-admin-code-response';
import { User } from '../Models/user';
import { UserType } from '../Models/user-type';
import { ApiService } from '../services/api.service';
import { HttpOptionService } from '../services/http-option.service';
@Injectable({
  providedIn: 'root',
})

/**使用者登入登出、取得已登入的使用者資訊 */
export class AuthService {
  private gusetUser: User = {
    userType: UserType.訪客,
    isAdmin: false,
  };
  private currentUserKey = 'currentUser'; // localStorage 的 key
  /**目前登入的使用者資訊 */
  private subUser$: BehaviorSubject<User>;

  /**設備碼 */
  private deviceCode: string;
  private verifyCode: string;

  /**login() 管理者需2步驟驗證(email發送驗證碼)，IsAdmin=1時將會傳送此參數*/
  private token2FA: string;
  private apiCode: string;

  constructor(
    private httpOptionService: HttpOptionService,
    private router: Router,
    private apiService: ApiService
  ) {
    // 設定全域登出函數
    (window as any).angularLogout = () => {
      this.logout();
    };

    // 從 localStorage 中恢復使用者資訊
    const storedUser = localStorage.getItem(this.currentUserKey);
    if (storedUser) {
      let loginedUser: User = JSON.parse(storedUser);
      this.subUser$ = new BehaviorSubject<User>(loginedUser);

      this.httpOptionService;
      this.setLoginedUser(
        loginedUser,
        this.httpOptionService.getHttpHeaderWebAPICode(),
        this.httpOptionService.getHttpHeaderWebAPIToken()
      );
    } else {
      this.subUser$ = new BehaviorSubject<User>(this.gusetUser);

      // 初始化時更新狀態
      this.setLoginedUser(this.gusetUser, '', '');
    }

    this.deviceCode = '';
    this.verifyCode = '';
    this.token2FA = '';
    this.apiCode = '';
  }

  /**使用者登入 */
  login(data: ReqLoginContract): Observable<LoginContract> {
    return this.getDeviceCode().pipe(
      switchMap((deviceCode: string) => {
        return this.apiService.httpPost('Auth/login', {
          loginId: data.loginId,
          passwd: data.passwd,
          verifyKey: data.verifyKey,
          verifyCode: this.verifyCode,
          deviceCode: deviceCode,
        });
      }),
      tap((data: LoginContract) => {
        if (data.status == 1 && data.reloadVerifyCode != 1) {
          this.setLoginedUser(
              {
                userType: data.type,
                isAdmin: data.IsAdmin == 1,
              },
              data.code,
              data.token
            );
            this.token2FA = '';
        }
      })
    );
  }

  /**使用者登出 */
  logout() {
    if (this.subUser$.value.userType != this.getGusetUser().userType) {
      this.apiService.httpGet('Auth/logout').subscribe((result) => {});
    }

    this.setLoginedUser(this.gusetUser, '', '');
    this.token2FA = '';
    this.router.navigate(['login']);
  }

  /**忘記密碼 */
  forgetPasswd(userEmail: string) {
    this.logout();
  }

  /**設定已登入的使用者資訊 */
  private setLoginedUser(user: User, apiCode: string, apiToken: string) {
    this.httpOptionService.setHttpHeaderCodeAndToken(apiCode, apiToken);

    this.subUser$.next(user);
    // 將使用者資訊儲存到 localStorage
    localStorage.setItem(this.currentUserKey, JSON.stringify(user));
    this.updateAuthLinksStatus();
  }

  private updateAuthLinksStatus() {
    // 確保 window.updateAuthLinks 存在
    if (typeof (window as any).updateAuthLinks === 'function') {
      (window as any).updateAuthLinks(this.isLogined());
    }
  }

  /**取得設備碼，如果設備碼不存在，則會重新取得設備碼 */
  private getDeviceCode(): Observable<string> {
    if (this.deviceCode) {
      return of(this.deviceCode);
    } else {
      return this.apiService.httpGet('Auth/deviceCode').pipe(
        map((data: DeviceCodeResponse) => {
          this.deviceCode = data.deviceCode;
          return this.deviceCode;
        })
      );
    }
  }

  /**取得目前使用者資訊 */
  getCurrentUserValue() {
    return this.subUser$.value;
  }

```

