
> https://angular.tw/guide/form-validation#validating-input-in-template-driven-forms

以`required`為例

1. 先在有標記`[(ngModel)]`的輸入欄位設定一個樣板變數(`#blNoField`)，且該樣板變數固定設為`ngModel`，並且標記HTML屬性`required`：
```html
<input
		type="text"
		[(ngModel)]="blNo.BlNo"
		class="form_control_EW"
		placeholder="BL no."
		#blNoField="ngModel"
		required
	  />
```

2. 在輸入欄位底下建立以下HTML，`blNoField`為樣板變數：
```html
<div
		*ngIf="
		  blNoField.invalid &&
		  (blNoField.dirty || blNoField.touched)
		"
		class="error-messages"
	  >
		<div *ngIf="blNoField.errors?.['required']">
		  <p class="memo">請輸入 BL no.</p>
		</div>
	  </div>
```

3. 提交表單前需驗證底下的輸入欄位的資料是否有符合驗證規則：
需在`form`̠表單內建立樣板變數(`#normalform`)，並固定設定為`ngForm`，然後呼叫自定義的提交表單方法`getDataFromBLNo(normalform)`
```html
<form id="normalform" name="normalform" #normalform="ngForm">
<div class="row margin_0">
  <div class="col-12">
	<div class="row">
	  <!-- 動態 BL No. 欄位 -->
	  <ng-container
		*ngFor="let blNo of blNumbers; let i = index"
	  >
		<div class="col-6 col-xs-8 col-lg-6 form_upload_EW">
		  <div class="row">
			<div class="col-12 col-md-6">
			  <input
				type="text"
				[(ngModel)]="blNo.BlNo"
				class="form_control_EW"
				placeholder="BL no."
				#blNoField="ngModel"
				required
			  />
			  <div
				*ngIf="
				  blNoField.invalid &&
				  (blNoField.dirty || blNoField.touched)
				"
				class="error-messages"
			  >
				<div *ngIf="blNoField.errors?.['required']">
				  <p class="memo">請輸入 BL no.</p>
				</div>
			  </div>
			</div>
			<div class="col-12 col-md-6">
			  <input
				type="text"
				[(ngModel)]="blNo.PinCode"
				class="form_control_EW"
				placeholder="PinCode"
				#pinCodeField="ngModel"
				required
			  />
			  <div
				*ngIf="
				  pinCodeField.invalid &&
				  (pinCodeField.dirty || pinCodeField.touched)
				"
				class="error-messages"
			  >
				<div *ngIf="pinCodeField.errors?.['required']">
				  <p class="memo">請輸入 PinCode</p>
				</div>
			  </div>
			</div>
		  </div>
		</div>
		<div class="col-2 col-xs-1 col-lg-4"></div>
		<div class="clearfix"></div>
	  </ng-container>
	</div>
  </div>
  <div class="clearfix"></div>
</div>
</form>

<div class="normal_button_EW mx-auto">
  <a href="javascript:void(0)" (click)="getDataFromBLNo(normalform)"
    >下一步<i class="fas fa-chevron-right"></i
  ></a>
</div>
```

```typescript
//提交欄位資料
getDataFromBLNo(form: NgForm) {
    if (form.valid == false) {
      markFormGroupTouched(form);
      return;
    }
}

markFormGroupTouched(form: NgForm){
	Object.keys(form.controls).forEach((key) => {
    const control = form.controls[key];
    control.markAsTouched();
    control.markAsDirty();
}
```

4. 完整案例如下：
> 如果該輸入欄位是會動態重覆產生的，無須擔心會因為產生的樣板變數重複，而導致輸入欄位判斷失誤的問題
```html
<!-- 動態 BL No. 欄位 -->
<ng-container
*ngFor="let blNo of blNumbers; let i = index"
>
<label
  class="col-4 col-xs-3 col-lg-2"
  [attr.for]="'normalform-name-' + i"
>
  BL no. <small *ngIf="i === 0">*</small>
</label>
<div class="col-6 col-xs-8 col-lg-6 form_upload_EW">
  <div class="row">
	<div class="col-12 col-md-6">
	  <input
		type="text"
		[id]="'BlNo-' + i"
		[name]="'BlNo-' + i"
		[(ngModel)]="blNo.BlNo"
		class="form_control_EW"
		placeholder="BL no."
		#blNoField="ngModel"
		required
	  />
	  <div
		*ngIf="
		  blNoField.invalid &&
		  (blNoField.dirty || blNoField.touched)
		"
		class="error-messages"
	  >
		<div *ngIf="blNoField.errors?.['required']">
		  <p class="memo">請輸入 BL no.</p>
		</div>
	  </div>
	</div>
	<div class="col-12 col-md-6">
	  <input
		type="text"
		[id]="'PinCode-' + i"
		[name]="'PinCode-' + i"
		[(ngModel)]="blNo.PinCode"
		class="form_control_EW"
		placeholder="PinCode"
		#pinCodeField="ngModel"
		required
	  />
	  <a
		*ngIf="i > 0"
		href="javascript:void(0)"
		class="btn_del"
		title="刪除"
		(click)="removeBlNumber(i)"
		><i class="far fa-trash-alt"></i
	  ></a>
	  <div
		*ngIf="
		  pinCodeField.invalid &&
		  (pinCodeField.dirty || pinCodeField.touched)
		"
		class="error-messages"
	  >
		<div *ngIf="pinCodeField.errors?.['required']">
		  <p class="memo">請輸入 PinCode</p>
		</div>
	  </div>
	</div>
  </div>
</div>
<div class="col-2 col-xs-1 col-lg-4"></div>
<div class="clearfix"></div>
</ng-container>
```
