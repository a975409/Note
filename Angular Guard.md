```
ng generate guard [guard名稱]
```

[無元件路由](https://angular.tw/api/router/Route#componentless-routes)

1. 產生的Guard
```typescript
import { CanActivateFn, Router } from '@angular/router';
export const driverGuard: CanActivateFn = (route, state) => {
  
};
```

2. 定義Guard的邏輯
```typescript
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from './auth.service';
import { UserType } from './Models/user-type';
export const driverGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  let currentUser = authService.getCurrentUserValue();
  if (currentUser.userType == UserType.司機) {
    console.log('司機已登入');
    return true;
  } else {
    return router.parseUrl('/login');
  }
};
```

3. 將Guard設定套用在Route上
```typescript
import { Routes } from '@angular/router';
import { DriverDashboardComponent } from './driver-dashboard/driver-dashboard.component';
import { driverGuard } from './driver.guard';
import { LoginComponent } from './login/login.component';
export const routes: Routes = [
  {
    path: 'driver',
    component: DriverDashboardComponent,

    //設定該路由的Guard
    canActivate: [driverGuard],
  },
  { path: 'login', component: LoginComponent },
  { path: '', redirectTo: '/login', pathMatch: 'full' },
  { path: '**', redirectTo: '/login' },
];
```
