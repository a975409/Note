> 每個[[Angular 元件(Component)(NgModule開發架構)]]都有一個 HTML 樣板，用於宣告該組件的渲染方式。你可以行內它或用檔案路徑定義此樣板。

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
```html
<p
  [id]="sayHelloId"
  [style.color]="fontColor">
  You can set my color in the component!
</p>
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

>括號：可在[[Angular 元件(Component)(NgModule開發架構)]]中設定觸發事件後所執行的function

sayMessage()是在[[Angular 元件(Component)(NgModule開發架構)]]中定義的：
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
#### *ngIF指令
```html
<h2>Hello World: ngIf!</h2>

<button type="button" (click)="onEditClick()">Make text editable!</button>

<div *ngIf="canEdit; else noEdit">
    <p>You can edit the following paragraph.</p>
</div>

<ng-template #noEdit>
    <p>The following paragraph is read only. Try clicking the button!</p>
</ng-template>

<p [contentEditable]="canEdit">{{ message }}</p>
```

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'hello-world-ngif',
  templateUrl: './hello-world-ngif.component.html'
})
export class HelloWorldNgIfComponent {
  message = "I'm read only!";
  canEdit = false;

  onEditClick() {
    this.canEdit = !this.canEdit;
    if (this.canEdit) {
      this.message = 'You can edit me!';
    } else {
      this.message = "I'm read only!";
    }
  }
}
```