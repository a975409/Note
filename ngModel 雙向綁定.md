
app.component.ts：
```typescript
import { Component } from '@angular/core';

//需新增以下語法
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-root',

  //需引入FormsModule
  imports: [FormsModule],
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {

  /**
   * 綁定畫面中的「名稱」欄位
   *
   * @memberof AppComponent
   */
  name = '';

  /**
   * 綁定畫面中的「內容」欄位
   *
   * @memberof AppComponent
   */
  content = '';

}
```

app.component.html：
```html
<form>
  <p>
    名稱：
    <input
      type="text"
      name="name"
      [(ngModel)]="name"
    >
  </p>

  <p>
    內容：
    <input
      type="text"
      name="content"
      [(ngModel)]="content"
    >
  </p>

  <p>
    <button type="submit">新增留言</button>
  </p>
</form>

<p>
  留言人： {{ name }}
</p>

<p>
  訊息內容： {{ content }}
</p>

<p>
  時間：
</p>
```

> 在 `<form></form>` 裡面用 `[(ngModel)]` 這個雙向綁定的 Directive 的話，input 的欄位上就要有 `name` 的屬性，或是加上 `[ngModelOptions]="{standalone: true}"` 也可以