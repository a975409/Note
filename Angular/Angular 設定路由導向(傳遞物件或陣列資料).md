在`app.routes.ts`設定路由：
```typescript
import { Routes } from '@angular/router';
import { CYFullPickupDataApplyQueryComponent } from './businessComponents/cyfull-pickup-data-apply-query/cyfull-pickup-data-apply-query.component';
import { CYFullPickupDataApplySubmitComponent } from './businessComponents/cyfull-pickup-data-apply-submit/cyfull-pickup-data-apply-submit.component';

//路由路徑在此新增
export const routes: Routes = [
  {
    path: 'CYFullPickupDataApplyQuery',
    component: CYFullPickupDataApplyQueryComponent,
    canActivate: [truckDealerAndDriverGuard],
  },
  {
    path: 'CYFullPickupDataApplySubmit',
    component: CYFullPickupDataApplySubmitComponent,
    canActivate: [truckDealerAndDriverGuard],
  },
];
```

cyfull-pickup-data-apply-query.component.ts，將`searchFormData`傳遞至`CYFullPickupDataApplySubmit`：
```typescript
import { GetCYFullPickupSearchRequest } from '../../Contracts/RequestDto/get-cyfull-pickup-search-request';

//新增這段程式碼
import { Router, RouterModule } from '@angular/router';

@Component({
  selector: 'app-housing-location',

  //引入RouterModule
  imports: [RouterModule],
  templateUrl: './housing-location.component.html',
  styleUrl: './housing-location.component.css',
})
export class HousingLocationComponent {
constructor(private router: Router) {}

 submitSearchForm(){
  let searchFormData: GetCYFullPickupSearchRequest[] = [];

  //searchFormData 可為陣列或物件等資料
  this.router.navigate(['CYFullPickupDataApplySubmit'], {
      state: { dataList: searchFormData },
    });
 }
}
```

cyfull-pickup-data-apply-submit.component.ts，接收傳遞進來的`dataList`的值：
```typescript
import { ActivatedRoute, Router, RouterModule } from '@angular/router';
import { GetCYFullPickupSearchRequest } from '../../Contracts/RequestDto/get-cyfull-pickup-search-request';

@Component({
  selector: 'app-cyfull-pickup-data-apply-submit',
  imports: [RouterModule],
  templateUrl: './cyfull-pickup-data-apply-submit.component.html',
  styleUrl: './cyfull-pickup-data-apply-submit.component.css',
})
export class CYFullPickupDataApplySubmitComponent
{
  constructor(
    private router: Router,
  ) {
    const navigation = this.router.getCurrentNavigation();
    if (navigation?.extras.state) {
      this.searchDto = navigation.extras.state[
        'dataList'
      ] as GetCYFullPickupSearchRequest[];
    }
  }
```

如何判斷是否有接收到指定的`state`值，範例如下：
```typescript
const navigation = this.router.getCurrentNavigation();

if (navigation?.extras.state 
	&& 'responseData' in navigation.extras.state) {
  this.responseData = navigation.extras.state[
	'responseData'
  ] as ExportInspectionFeeGetCtnrListResponse;
} else {
  this.getData();
}
```
