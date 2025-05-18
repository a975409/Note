```typescript
//引入Router
import { Router } from '@angular/router';

constructor(private router: Router){}

//導向方式如下：
this.router.navigate([`CYEmptyBookingApplySubmit/${queryType}/${this.applyNo}`]);

//或
this.router.parseUrl('login');
```

