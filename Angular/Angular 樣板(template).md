> 每個[[Angular 元件(Component)(NgModule開發架構)]]都有一個 HTML 樣板，用於宣告該組件的渲染方式。你可以行內它或用檔案路徑定義此樣板。

[[Angular 樣板(template) 顯示非同步的資料]]

HTML 樣板：
```html
<p>{{ message }}</p>
```

這裡 message 的值來自元件類別：
```typescript
import { Component } from '@angular/core';

@Component ({
  selector: 'hello-world-interpolation',
  templateUrl: './hello-world-interpolation.component.html'
})
export class HelloWorldInterpolationComponent {
    message = 'Hello, World!';
}
```

當應用載入元件及其樣板時，使用者將看到以下內容：
```html
<p>Hello, World!</p>
```

#### 支援屬性繫結，可設定 HTML 元素的 Property 和 Attribute 的值：
[[HTML Attribute與Property的差別]]

Property繫結：
```html
<p
  [id]="sayHelloId"
  [style.color]="fontColor">
  You can set my color in the component!
</p>
```

Attribute繫結(前面須加上attr.)：
```html
<p [attr.attribute-you-are-targeting]="expression"></p>
```

>方括號：可在[[Angular 元件(Component)(NgModule開發架構)]]中設定 HTML Property 或 Attribute 的值。
``` typescript
import { Component } from '@angular/core';

@Component ({
  selector: 'hello-world-bindings',
  templateUrl: './hello-world-bindings.component.html'
})
export class HelloWorldBindingsComponent {
  sayHelloId = 1;
  fontColor = 'blue';
}
```

`ngClass`
```html
<td
  [ngClass]="{
	txt_red_EW:
	  ApplyApprovedBKGInfo.Imdg ||
	  ApplyApprovedBKGInfo.Unno ||
	  ApplyApprovedBKGInfo.Subsidiary ||
	  ApplyApprovedBKGInfo.MP
  }"
>
  {{
	ApplyApprovedBKGInfo.Imdg ||
	ApplyApprovedBKGInfo.Unno ||
	ApplyApprovedBKGInfo.Subsidiary ||
	ApplyApprovedBKGInfo.MP
	  ? "Y"
	  : "N"
  }}
</td>
```
#### 設定監聽事件
[[DOM 事件]]
```html
<button
  type="button"
  [disabled]="canClick"
  (click)="sayMessage()">
  Trigger alert message
</button>
```

>括號：可在Component中設定觸發事件後所執行的function

```typescript
import { Component } from '@angular/core';

@Component ({
  selector: 'hello-world-bindings',
  templateUrl: './hello-world-bindings.component.html'
})
export class HelloWorldBindingsComponent {
  canClick = false;
  message = 'Hello, World';

  sayMessage() {
    alert(this.message);
  }
}
```

#### ngModel 雙向繫結：
component需引入：
```typescript
import {FormsModule} from '@angular/forms'

export class Hero {

  constructor(
    public id: number,
    public name: string,
    public power: string,
    public alterEgo?: string
  ) {  }
}
```

搭配HTML input 使用：
```html
<input type="text" class="form-control" id="name" ([ngModel])="model.name" name="name">
```

[[Angular ngIF]]