
https://angular.tw/guide/reactive-forms#creating-dynamic-forms

form表單內可輸入多個欄位，可新增或移除指定欄位，回傳至後端時，會組成陣列資料
HTML 模板：
```HTML
<form [formGroup]="applyForm" (ngSubmit)="submitSignUp()">
<ul>
  <li formArrayName="CarNolist" [style.display]="canAddCarNo()">
	<div *ngFor="
		let carNo of carNoArray.controls;
		let i = index
	  ">
	  <input
		type="text"
		[formControlName]="i"
		name="CarNo"
		class="login_style"
		placeholder="車牌號碼"
	  />
	  <a
		href="javascript:void(0)"
		class="btn_del"
		title="刪除"
		*ngIf="i > 0"
		(click)="removeCarNo(i)"
		><i class="far fa-trash-alt"></i
	  ></a>
	</div>
  </li>
  <li
	class="form_upload_EW"
	[style.display]="canAddCarNo()"
  >
	<button type="button" (click)="addCarNo()">
	  <i class="fas fa-plus"></i>新增車牌
	</button>
  </li>
</ul>
</form>
```

Component 元件：
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
  imports: [
    CommonModule,
    ReactiveFormsModule
  ],
  templateUrl: './signup.component.html',
  styleUrl: './signup.component.css',
})
export class SignupComponent {
  applyForm: FormGroup;
  CarNolist: FormArray;

  constructor(
    private enumOptionService: EnumOptionService,
    private fb: FormBuilder,
    private apiService: ApiService
  ) {
 
    this.CarNolist = this.fb.array([this.fb.control('')]);

    this.applyForm = this.fb.group({
      CarNolist: this.CarNolist,
    });
  }

  /**是否能設定車牌 */
  canAddCarNo(): string {
    if (this.userType.value == UserType.司機) return '';
    else return 'none';
  }

  /**取得多筆已輸入的車牌欄位 */
  get carNoArray() {
    return this.applyForm.get('CarNo') as FormArray;
  }

  /**新增車牌欄位 */
  addCarNo() {
    this.carNoArray.push(this.fb.control('', [Validators.required])); // 新增一個空的 FormControl

    // 在設定完驗證規則後，需更新驗證器
    this.carNoArray.updateValueAndValidity();

    // 標記所有必填欄位為 touched，這樣就會立即顯示錯誤訊息
    this.carNoArray.controls.forEach((control) => control.markAsTouched());
  }

  /**移除特定的車牌欄位 */
  removeCarNo(index: number) {
    this.carNoArray.removeAt(index);
  }
  
  /**移除所有車牌欄位 */
  resetCarNo() {
    while (this.carNoArray.length) {
      this.removeCarNo(0);
    }
  }
}

```