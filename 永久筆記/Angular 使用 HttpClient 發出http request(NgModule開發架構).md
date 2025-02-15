在根模組 `AppModule` 中匯入HttpClient模組：
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

//新增這段
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [
    BrowserModule,
    
	//新增這段
    HttpClientModule,
  ],
  declarations: [
    AppComponent,
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule {}
```

其他服務可直接引用HttpClient模組，發出http request：
```typescript
import { Injectable } from '@angular/core';

//新增這段
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root',
})
export class CartService {
  constructor(private http: HttpClient) {}

  //發出http request
  getShippinjgPrices() {
    return this.http.get<{ type: string; price: number }[]>(
      '/assets/shipping.json'
    );
  }
}
```

>使用 [`HttpClient.get()`](https://angular.tw/api/common/http/HttpClient#get) 方法從伺服器獲取資料。該非同步方法會發送一個 HTTP 請求，並回傳一個 Observable，範例如下：
```typescript
import { Component, OnInit } from '@angular/core';
import { Observable } from 'rxjs';
import { CartService } from '../cart.service';
@Component({
  selector: 'app-shipping',
  templateUrl: './shipping.component.html',
  styleUrls: ['./shipping.component.css'],
})
export class ShippingComponent implements OnInit {
  constructor(private cartService: CartService) {}

  //將shippingCosts變數設為Observable<>，以接收http request的response結果
  shippingCosts!: Observable<{ type: string; price: number }[]>;

  ngOnInit(): void {
    //接收http request的response結果
    this.shippingCosts = this.cartService.getShippinjgPrices();
  }
}
```

[[Angular 樣板(template) 顯示非同步的資料]]

shipping.json：
```JSON
[
  {
    "type": "Overnight",
    "price": 25.99
  },
  {
    "type": "2-Day",
    "price": 9.99
  },
  {
    "type": "Postal",
    "price": 2.99
  }
]
```
