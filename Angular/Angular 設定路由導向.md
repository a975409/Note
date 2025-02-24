在`app.routes.ts`設定路由：
```typescript
import { Routes } from '@angular/router';

import { DetailsComponent } from './details/details.component';
import { HomeComponent } from './home/home.component';

//路由路徑在此新增
export const routes: Routes = [
  {
    path: 'details/:id',
    component: DetailsComponent,
    title: 'Home details',
  },
];
```

在 housing-location.component.ts 引入 RouterModule：
```typescript
import { Component, Input } from '@angular/core';
import { HousingLocation } from '../housinglocation';

//新增這段程式碼
import { RouterModule } from '@angular/router';


@Component({
  selector: 'app-housing-location',

  //引入RouterModule
  imports: [RouterModule],
  templateUrl: './housing-location.component.html',
  styleUrl: './housing-location.component.css',
})
export class HousingLocationComponent {
  @Input() housingLocation!: HousingLocation;
}

```

在 housing-location.component.html 設定連結(routerLink)：
```html
<section class="listing">
  <img
    class="listing-photo"
    [src]="housingLocation.photo"
    alt="Exterior photo of {{ housingLocation.name }}"
  />
  <h2 class="listing-heading">{{ housingLocation.name }}</h2>
  <p class="listing-location">
    {{ housingLocation.city }}, {{ housingLocation.state }}
  </p>
  <a [routerLink]="['/details', housingLocation.id]">Learn More</a>
</section>

```

在 details.component.ts 取得路由參數：
```typescript
import { Component } from '@angular/core';
import { HousingService } from '../housing.service';
import { HousingLocation } from '../housinglocation';

//需新增以下程式碼
import { ActivatedRoute } from '@angular/router';
@Component({
  selector: 'app-details',
  imports: [],
  templateUrl: './details.component.html',
  styleUrl: './details.component.css',
})
export class DetailsComponent {
  constructor(
    private route: ActivatedRoute,
    private housingService: HousingService
  ) {

    //取得路由參數
    this.housingLocationId = Number(this.route.snapshot.params['id']);
    this.housingLocation = housingService.getHousingLocationById(
      this.housingLocationId
    );
  }
  housingLocation: HousingLocation | undefined;
  housingLocationId = -1;
}

```

app.component.html一定要加上以下這段，這樣路由頁面切換才會有效：
```html
<router-outlet></router-outlet>
```

[ActivatedRoute](https://angular.tw/api/router/ActivatedRoute)：Angular 路由器載入的每個元件都有自己專屬的ActivatedRoute，ActivatedRoute內包含有關路由和路由引數的訊息