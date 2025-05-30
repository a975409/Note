[表單驗證](https://angular.tw/guide/form-validation)
[Validators：如何動態更新表單驗證器](https://hackmd.io/@Heidi-Liu/angular-validators)
[angular內建的驗證器](https://angular.tw/api/forms/Validators)

#### 使用內建的驗證器
```typescript
this.userTel = new FormControl('', [Validators.required]);
```

```html
<li>
<input
  type="tel"
  name="userTel"
  id="userTel"
  formControlName="userTel"
  class="login_style"
  placeholder="聯絡電話"
/>
<!-- 錯誤訊息顯示 -->
<div
  *ngIf="
	userTel?.errors &&
	(userTel?.dirty || userTel?.touched)
  " class="error-messages">

  <!--指定驗證器 -->
  <small *ngIf="userTel?.errors?.['required']">
	請輸入電話
  </small>
</div>
</li>
```

#### 使用自訂的驗證器
```typescript
    this.passwd = new FormControl('', [passwordValidator()]);
```

自訂驗證器 passwordValidator：
```typescript
export function passwordValidator(): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {

    //取得輸入值
    const value: string = control.value || '';

	// 如果欄位為空，不進行驗證（可以搭配 required 驗證器）
    if (!value) {
      return null;
    }

    // 定義驗證規則
    const minLength = 8;
    const maxLength = 30;
    const hasUpperCase = /[A-Z]/.test(value);
    const hasLowerCase = /[a-z]/.test(value);
    const hasNumber = /[0-9]/.test(value);
    const hasSpecialChar = /[!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]/.test(value);
    const hasInvalidChar =
      /[^A-Za-z0-9!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]/.test(value);

    // 檢查長度
    const isLengthValid =
      value.length >= minLength && value.length <= maxLength;

    // 收集錯誤
    const errors: ValidationErrors = {};

    if (!isLengthValid) {
      errors['length'] = {
        required: `${minLength}-${maxLength}`,
        actual: value.length,
      };
    }

    if (!hasUpperCase) {
      errors['upperCase'] = true;
    }

    if (!hasLowerCase) {
      errors['lowerCase'] = true;
    }

    if (!hasNumber) {
      errors['number'] = true;
    }

    if (!hasSpecialChar) {
      errors['specialChar'] = true;
    }

    if (hasInvalidChar) {
      errors['invalidChar'] = true;
    }

    //回傳null代表驗證通過
    return Object.keys(errors).length > 0 ? errors : null;
  };
}
```

```html
<li>
<input
  type="password"
  name="passwd"
  id="passwd"
  formControlName="passwd"
  class="login_style"
  placeholder="密碼"
/>

<!-- 錯誤訊息顯示 -->
<div
  *ngIf="passwd.errors && (passwd.dirty || passwd.touched)"
  class="error-messages">
  <small *ngIf="passwd.errors?.['length']">
	密碼長度必須在 8-30 個字元之間
  </small>
  <small *ngIf="passwd.errors?.['upperCase']">
	密碼必須包含至少一個大寫字母
  </small>
  <small *ngIf="passwd.errors?.['lowerCase']">
	密碼必須包含至少一個小寫字母
  </small>
  <small *ngIf="passwd.errors?.['number']">
	密碼必須包含至少一個數字
  </small>
  <small *ngIf="passwd.errors?.['specialChar']">
	密碼必須包含至少一個特殊符號
  </small>
  <small *ngIf="passwd.errors?.['invalidChar']">
	密碼包含不允許的字元
  </small>
</div>
<p class="memo txt_blue_EW">
  ※ 密碼限 8-30 字元，最少包含 1 個大寫字母、1
  個小寫字母、1 個數字與 1
  個常見特殊符號（如：!、#、$、^、*……等），其他字元不接受。
</p>
</li>
```

#### 更新現有`FormControl`驗證器
```typescript
PAYAT: FormControl;
this.PAYAT.clearValidators();
this.PAYAT.reset();
if (this.userType.value == UserType.報關行) {
  this.PAYAT.addValidators(Validators.required);
}

// 在設定完驗證規則後，需更新驗證器
this.PAYAT.updateValueAndValidity();

//觸發該欄位的驗證器，這樣就會立即顯示錯誤訊息
this.PAYAT.markAsTouched();
```

#### 觸發`FormGroup`底下所有`FormControl`的驗證器，會立即顯示所有輸入欄位的錯誤訊息
```typescript
this.searchDto = this.fb.array([]);
this.applyForm = this.fb.group({ DataList: this.searchDto });

//觸發`FormGroup`底下所有`FormControl`的驗證器，會立即顯示所有輸入欄位的錯誤訊息
//this.applyForm.invalid=>檢查底下所有FormControl是否通過驗證，未通過則回傳true
 if (this.applyForm.invalid) {
      this.applyForm.markAllAsTouched();
      return;
 }
```

#### 清除指定的欄位驗證失敗的錯誤訊息
```typescript
const dataListArray = this.applyForm.get('DataList') as FormArray;
dataListArray.controls.forEach((group, index) => {
  const cntrNoControl = group.get('cntrNo') as FormControl;

  //檢查錯誤訊息是否符合以下條件
  if (
	cntrNoControl.errors?.['invalidFormat'] ||
	cntrNoControl.errors?.['invalidCheckDigit']
  ) {
    //清除該欄位錯誤訊息，需設為null
	cntrNoControl.setErrors(null);
  }
});
```
