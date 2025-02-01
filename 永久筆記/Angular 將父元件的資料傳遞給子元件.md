父元件(Component)：product-list
子元件(Component)：product-alerts

#### 子元件(Component)：product-alerts
product-alerts.component.ts：
``` Typescript
/* Component後面再新增一個Input */
import { Component, Input } from '@angular/core';

/*引入Product interface*/
import { Product } from '../products';

@Component({
  selector: 'app-product-alerts',
  templateUrl: './product-alerts.component.html',
  styleUrls: ['./product-alerts.component.css'],
})

export class ProductAlertsComponent {

  /*新增一個 product 變數，設定為@Input()，代表其變數資料要從父元件傳入*/
  @Input() !product: Product;
}
```

>product變數名稱前加上 `!`，代表必須傳入該值，它告訴 TypeScript 編譯器此屬性的值不能是 `null` 或 `undefined`。

product-alerts.component.html：
```html
<p *ngIf="product && product.price > 700">
  <button type="button">Notify Me</button>
</p>
```

#### 父元件(Component)：product-list
product-list.component.html：
```html
<div *ngFor="let product of products">
 <!-- 將product代入至子元件 -->
  <app-product-alerts [product]="product"></app-product-alerts>
</div>
```

#### products.ts：
```typescript
export interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
}

export const products = [
  {
    id: 1,
    name: 'Phone XL',
    price: 799,
    description: 'A large phone with one of the best screens'
  },
  {
    id: 2,
    name: 'Phone Mini',
    price: 699,
    description: 'A great phone with one of the best cameras'
  },
  {
    id: 3,
    name: 'Phone Standard',
    price: 299,
    description: ''
  }
];
```