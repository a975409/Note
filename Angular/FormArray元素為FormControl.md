
https://angular.tw/guide/reactive-forms#creating-dynamic-forms

運用情境：
>會動態建立輸入欄位，就會以FormArray元素內包含多個FormControl

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
  constructor(private fb: FormBuilder) {
 
    this.CarNolist = this.fb.array([this.fb.control('')]);
    this.applyForm = this.fb.group({
      CarNolist: this.CarNolist,
    });
  }
  
  applyForm: FormGroup;
  CarNolist: FormArray;

  /**取得多筆已輸入的車牌欄位 */
  get carNoArray() {
    return this.applyForm.get('CarNolist') as FormArray;
  }

  /**新增車牌欄位 */
  addCarNo() {
    const dataListArray = this.applyForm.get('CarNolist') as FormArray
    let index = dataListArray.length;

    // 新增一個空的 FormControl
    //方法一：往下新增
    this.carNoArray.push(this.fb.control('', [Validators.required])); 

    //方法二：指定index新增
    dataListArray.insert(index, this.fb.control('', [Validators.required]));
  }

  /**移除特定的車牌欄位 */
  removeCarNo(index: number) {
    this.carNoArray.removeAt(index);
  }
}
```

> 如上所述，applyForm(`FormGroup`)->CarNolist(`FormArray`)->多個`FormControl`
> Html樣板要將(`FormArray`)裡面多個`FormControl`元素設定雙向繫結，則需要依階層往下指定
> 
> 以下方Html樣板為例：
> `[formGroup]="applyForm"`->`formArrayName="CarNolist"`->`[formControlName]="i"`
> 其中要繫結`FormArray`裡面多個`FormControl`元素，則設定為`[formControlName]="i"`

HTML 模板：
```HTML
<form [formGroup]="applyForm" (ngSubmit)="submitSignUp()">
<ul>
  <li formArrayName="CarNolist">
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
		(click)="removeCarNo(i)"
		><i class="far fa-trash-alt"></i
	  ></a>
	</div>
  </li>
  <li class="form_upload_EW">
	<button type="button" (click)="addCarNo()">
	  <i class="fas fa-plus"></i>新增車牌
	</button>
  </li>
</ul>
</form>
```

