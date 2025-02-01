
定義Form表單：
```typescript
import { Component } from '@angular/core';
import { CartService } from '../cart.service';

//從 @angular/forms 套件匯入 FormBuilder
import { FormBuilder } from '@angular/forms';

@Component({
  selector: 'app-cart',
  templateUrl: './cart.component.html',
  styleUrls: ['./cart.component.css'],
})

export class CartComponent {

  //在 CartComponent 的 constructor() 中注入 FormBuilder 服務
  constructor(
    private cartService: CartService,
    private formBuilder: FormBuilder
  ) {}

  items = this.cartService.getItems();

  //取得Form表單內，在input標籤內所輸入的值
  checkoutForm = this.formBuilder.group({ name: '', address: '' });

  //建立提交表單後所要執行的動作
  onSubmit(): void {
    this.items = this.cartService.clearCart();
    console.warn('Your order has been submitted', this.checkoutForm.value);
    this.checkoutForm.reset();
  }
}
```

在[[Angular 樣板(template)]]建立Form表單：
```html
<form [formGroup]="checkoutForm" (ngSubmit)="onSubmit()">
  <div>
    <label for="name"> Name </label>
    <input id="name" type="text" formControlName="name" />
  </div>

  <div>
    <label for="address"> Address </label>
    <input id="address" type="text" formControlName="address" />
  </div>

  <button class="button" type="submit">Purchase</button>
</form>
```

> 每個input 需設定 formControlName，另外Form標籤需設定formGroup