#### 建立服務
在終端中透過執行以下指令生成一個新的服務：
```
ng generate service [服務名稱]
```

執行後產生的服務.ts如下，檔名格式為[服務名稱].service.ts：
```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class CartService {
  constructor() {}
}
```

> 建立的Service的依賴注入，是以單例注入的方式註冊，也就是說，只要在其中有引用的地方設定該Service的參數，其他地方有引用該Service所讀取到的參數是已被設定過的

其他元件或服務，透過依賴注入的方式引用服務，以元件為例：
```typescript
import { Component} from '@angular/core';
import { Product, products } from '../products';
import { CartService } from '../cart.service';

@Component({
  selector: 'app-product-details',
  templateUrl: './product-details.component.html',
  styleUrls: ['./product-details.component.css'],
})

export class ProductDetailsComponent{
  //引用服務
  constructor(private cartService: CartService) {}

  addToCart(product: Product) {
    //呼叫該服務的function
    this.cartService.addToCart(product);
    window.alert('Your product has been added to the cart!');
  }
}
```
