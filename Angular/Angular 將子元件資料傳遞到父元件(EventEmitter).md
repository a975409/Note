父元件(Component)：product-list
子元件(Component)：product-alerts

#### 子元件(Component)：product-alerts
product-alerts.component.ts：
```typescript
/* 新增Output、EventEmitter */
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { Product } from '../products';

@Component({
  selector: 'app-product-alerts',
  templateUrl: './product-alerts.component.html',
  styleUrls: ['./product-alerts.component.css'],
})

export class ProductAlertsComponent {
  @Input() product: Product | undefined;

  /*新增一個 notify 變數，設定為@Output()，代表其變數資料要傳遞給父元件
  另外EventEmitter代表自定義事件*/
  @Output() notify = new EventEmitter();
}
```

[EventEmitter](https://angular.tw/api/core/EventEmitter)

product-alerts.component.html：
```html
<p *ngIf="product && product.price > 700">
  <!--變數名稱.emit()為固定寫法-->
  <button type="button" (click)="notify.emit()">Notify Me</button>
</p>
```

#### 父元件(Component)：product-list
product-list.component.html：
```html
<!--(notify)代表子元件的notify變數-->
<div *ngFor="let product of products">
  <app-product-alerts
    [product]="product"
    (notify)="onNotify()"
  ></app-product-alerts>
</div>
```

product-list.component.ts：
```typescript
import { Component } from '@angular/core';
import { products } from '../products';
@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css'],
})

export class ProductListComponent {
  products = [...products];
  
  /*定義onNotify function*/
  onNotify() {
    window.alert('You will be notified when the product goes on sale');
  }
}
```

#### 如果有輸入參數的話
子元件(Component)：
```typescript
export class PageComponent{
@Output() pageChange = new EventEmitter<number>();
pageChanged(page: number): void {
    this.pageChange.emit(page);
  }
}
```

子元件(Html)：
```html
<a class="page-link" href="javascript:void(0)" (click)="pageChanged(page)"></a>
```

父元件(Component)
component：
```typescript
selectPageSearchForm(page: number) {
    this.page.page = page;
    this.getResultdata();
  }
```

Html：
```html
<app-page
  (pageChange)="selectPageSearchForm($event)">
</app-page>
```