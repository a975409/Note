元件(Component)包括以下三個部分：
1. 一個 CSS 選擇器(selector)，用於定義如何在樣板中使用元件。樣板中與此選擇器對應的 HTML 元素將成為該組件的實例。
2. [[Angular 樣板(template)]]：一個 HTML 樣板，用於指示 Angular 如何渲染此元件
3. 一組可選的 CSS 樣式，用於定義樣板中 HTML 元素的外觀
4. imports：外部引入的元件(Component)

執行以下指令產生元件：
```
ng generate component [component名稱] --skip-tests
```

執行後會產生以下3個檔案：
- `[component名稱].component.ts`
- `[component名稱].component.html`
- `[component名稱].component.css`

`[component名稱].component.ts`的最小化內容(`NgModule`)：
```typescript
import { Component } from '@angular/core';

//假設有引用到其他元件，則要加上這段程式碼
import {引用元件} from '其他引用元件路徑'

@Component({
  selector: 'app-home',

  //假設有引用到其他元件，則需添加到 imports: [] 陣列變數內
  imports: [多個引用元件...],
  templateUrl: './home.component.html',
  styleUrl: './home.component.css'
})
export class HomeComponent {

}
```

> 只要樣板或元件內有引用到其他元件，則需添加到 imports: [] 陣列變數內，以及要額外import其他元件

要使用此元件，請在樣板中編寫以下內容：
```html
<app-home></app-home>
```

當 Angular 渲染此元件時，生成的 DOM 如下所示：
```html
<app-home>
   <!-- HTML內容-->
</app-home>
```

[元件生命週期](https://angular.tw/guide/lifecycle-hooks)