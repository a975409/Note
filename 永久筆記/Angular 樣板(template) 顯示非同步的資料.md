

需新增`async`關鍵字：
```html
<h3>Shipping Prices</h3>

<div class="shipping-item" *ngFor="let shipping of shippingCosts | async">
  <span>{{ shipping.type }}</span>
  <span>{{ shipping.price | currency }}</span>
</div>
```