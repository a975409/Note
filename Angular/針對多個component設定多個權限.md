app.routes：
```typescript
{
    path: 'ITTFEPickupDataList',
    component: ITTFEPickupDataListComponent,
    canActivate: [anyGuard(truckDealerGuard, driverGuard, unauthGuard)],
  },
```

any-guard.guard.ts：
```typescript
import { CanActivateFn } from '@angular/router';

// 創建一個組合函數，只要有一個守衛返回 true，就返回 true
export function anyGuard(...guards: CanActivateFn[]): CanActivateFn {
  return (route, state) => {
    for (const guard of guards) {
      const result = guard(route, state);
      // 如果任何一個守衛返回 true，就立即返回 true
      if (result === true) {
        return true;
      }
    }
    // 如果所有守衛都返回 false，則返回 false
    return false;
  };
}
```

unauth.guard.ts：
```typescript
import { inject } from '@angular/core';
import { CanActivateFn,Router } from '@angular/router';

export const unauthGuard: CanActivateFn = (route, state) => {
  const router = inject(Router);
  console.log('權限檢查失敗，導向登入頁');
  router.navigate(['/login']);
  return false;
};
```