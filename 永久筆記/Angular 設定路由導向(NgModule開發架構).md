在app.module.ts設定路由：
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
import { ReactiveFormsModule } from '@angular/forms';
import { AppComponent } from './app.component';
import { TopBarComponent } from './top-bar/top-bar.component';
import { ProductListComponent } from './product-list/product-list.component';
import { ProductAlertsComponent } from './product-alerts/product-alerts.component';
import { ProductDetailsComponent } from './product-details/product-details.component';

@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      
      //新增導向到 ProductDetailsComponent 的路由路徑，:productId代表路由參數
      { path: 'products/:productId', component: ProductDetailsComponent },
    ]),
  ],
  declarations: [
    AppComponent,
    TopBarComponent,
    ProductListComponent,
    ProductAlertsComponent,
    ProductDetailsComponent,
  ],
  bootstrap: [AppComponent],
})

export class AppModule {}
```

在 product-list.component.html 設定連結(routerLink)：
```html
<h2>Products</h2>
<div *ngFor="let product of products">
  <h3>
    <a
      [title]="product.name + ' details'"
      [routerLink]="['/products', product.id]"
      >{{ product.name }}</a>
  </h3>
</div>
```

在 product-details.component.ts 取得路由參數：
```typescript
//新增OnInit
import { Component, OnInit } from '@angular/core';
import { Product, products } from '../products';

//新增以下程式碼
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-product-details',
  templateUrl: './product-details.component.html',
  styleUrls: ['./product-details.component.css'],
})
export class ProductDetailsComponent implements OnInit {
  product: Product | undefined;
  constructor(private route: ActivatedRoute) {}

  //取得productId路由參數，並透過此參數取得資料
  ngOnInit() {
    const routeParams = this.route.snapshot.paramMap;
    const productIdFromRoute = Number(routeParams.get('productId'));

    this.product = products.find(
      (product) => product.id === productIdFromRoute
    );
  }
}

```

[OnInit](https://angular.tw/api/core/OnInit)：一個生命週期鉤子，它會在 Angular 初始化完了該指令的所有資料繫結屬性之後呼叫
[ActivatedRoute](https://angular.tw/api/router/ActivatedRoute)：Angular 路由器載入的每個元件都有自己專屬的ActivatedRoute，ActivatedRoute內包含有關路由和路由引數的訊息

implements：類別可以用`implements` 關鍵字去實作interface的方法