
### 常用表單基礎類
| 基底類別Base classes                                                          | 詳情                                                                                       | HTML應用情境                                                                                                                   |
| :------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| [[FormControl]]                                                           | 追蹤單個表單控制元件的值和驗證狀態。                                                                       | 單一input或select欄位<br>`<input type="email" name="userEmail" id="userEmail" formControlName="userEmail" placeholder="電子信箱"/>` |
| [FormGroup](https://angular.tw/api/forms/FormGroup)                       | 追蹤一個表單控制元件群組的值和狀態。                                                                       | 1.通常會對應一個HTML form表單<br>`<form [formGroup]="applyForm" (ngSubmit)="submitSignUp()"></form>`<br><br>2.將多個輸入欄位組成一個群組         |
| [[FormArray]]                                                             | 追蹤表單控制元件陣列的值和狀態。<br>(多個[FormControl](https://angular.tw/api/forms/FormControl) )         | 存放多個HTML的input或select<br>                                                                                                  |
| [ControlValueAccessor](https://angular.tw/api/forms/ControlValueAccessor) | 在 Angular 的 [FormControl](https://angular.tw/api/forms/FormControl) 實例和內建 DOM 元素之間建立一個橋樑 |                                                                                                                            |
|                                                                           |                                                                                          |                                                                                                                            |
> HTML屬性：`formControlName` 或 `[formControl]`，與根據名字將現有 [FormGroup](https://angular.tw/api/forms/FormGroup) 中的 [FormControl](https://angular.tw/api/forms/FormControl) 與一個表單控制元件進行同步。

[[Angular 表單驗證(一)]]
[[Angular 表單驗證(二) - FormGroup套用驗證器]]
[[Angular 指定要上傳的檔案]]
[[Angular 指定要上傳的檔案(不使用input type=file)]]

建立回應式表單：
component：
```typescript
import { CommonModule } from '@angular/common';
import { Component } from '@angular/core';
import {
  FormArray,
  FormBuilder,
  FormControl,
  FormGroup,
  ReactiveFormsModule,
} from '@angular/forms';
import { ApiService } from '../../commonServices/api.service';

@Component({
  selector: 'app-signup',

  //註冊回應式表單模組
  imports: [
    CommonModule,
    ReactiveFormsModule
  ],
  templateUrl: './signup.component.html',
  styleUrl: './signup.component.css',
})
export class SignupComponent {
  applyForm: FormGroup;
  favoriteColorControl:FormControl;

  constructor(
    private enumOptionService: EnumOptionService,
    private fb: FormBuilder,
    private apiService: ApiService
  ) {

    //新增一個FormControl
    this.favoriteColorControl = this.fb.array([this.fb.control('')]);   
    this.applyForm = this.fb.group({
      favoriteColorControl: this.favoriteColorControl,
    });
  }
}
```

HTML：
```html
<form [formGroup]="applyForm" (ngSubmit)="submitSignUp()">
<ul>
  <li>
    <!--在樣板中註冊對應的FormControl -->
	<input
		type="text"
		[formControlName]="favoriteColorControl"
		class="login_style"
		placeholder="車牌號碼"
	  />
  </li>
  <li>
	<button
	  type="submit"
	  class=""
	  value="確定註冊"
	  onclick="location.href='login.html'"
	>
	  確定註冊
	</button>
  </li>
</ul>
</form>
```

#### FormGroup
[建立巢狀的表單組](https://angular.tw/guide/reactive-forms#creating-nested-form-groups)
[更新部分資料模型](https://angular.tw/guide/reactive-forms#updating-parts-of-the-data-model)

可使用 FormBuilder 服務建立`formgroup`、`formcontrol`、`formarray`等控制元件：
```typescript
import { Component } from '@angular/core';
import { FormBuilder } from '@angular/forms';

@Component({
//註冊回應式表單模組
  imports: [
    CommonModule,
    ReactiveFormsModule
  ],
  selector: 'app-profile-editor',
  templateUrl: './profile-editor.component.html',
  styleUrls: ['./profile-editor.component.css']
})
export class ProfileEditorComponent {
  profileForm = this.fb.group({
    firstName: this.fb.control(''),
    lastName: this.fb.array([]),
    address: this.fb.group({
      street: [''],
      city: [''],
      state: [''],
      zip: ['']
    }),
  });

  constructor(private fb: FormBuilder) { }
}
```

指定`FormGroup`底下的`FormControl`或`FormArray`
```typescript

//`FormControl`
this.profileForm.get('firstName') as FormControl

//`FormArray`
this.profileForm.get('lastName') as FormArray

```

