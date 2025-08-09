[Angular ViewChild 與 ViewChildren 介紹](https://ithelp.ithome.com.tw/articles/10276814)

>component 藉由ViewChild 與 ViewChildren 指令，可存取[[Angular 樣板(template)]]上的HTML元素、component、樣板變數等等

#### ViewChild
> 存取第一筆符合的HTML元素、component、樣板變數等等

1. 存取[[Angular 樣板(template)]]上的component：
```html
  <!--CONTENT-->
  <content *ngIf="isDetail">
    <app-check-release-pin-page
      (btnAccess)="onBtnAccessChange($event)"
      (openAlert)="openAlert($event)"
      (openDialog)="openDialog($event)"
      [InputDataList]="returnArray"
      (setDataInfo)="setDataInfo($event)"
    ></app-check-release-pin-page>
  </content>
```

```typescript
import { CheckReleasePinPageComponent } from '../CheckReleasePin/check-release-pin-page/check-release-pin-page.component';

import { ViewChild } from '@angular/core';

@Component({
  selector: 'app-check-release-pin-detail',
  imports: [
    CheckReleasePinPageComponent,
  ],
  templateUrl: './check-release-pin-detail.component.html',
  styleUrl: './check-release-pin-detail.component.css',
})

export class CheckReleasePinDetailComponent {
  @ViewChild(CheckReleasePinPageComponent) pageComponent!: CheckReleasePinPageComponent;
}
```

2. 存取樣板變數
> 指定的樣板變數可為`NgModel`、`NgForm`、`HTMLInputElement`等等

以下以`NgModel`為例：
```html
<div class="col-12 col-md-8">
  <input
	type="text"
	id="applyNo"
	name="applyNo"
	class="form_control_EW"
	[placeholder]="
	  queryType == 'BookingNo'
		? '請輸入訂艙號碼'
		: '請輸入VIP SO no.'
	"
	[(ngModel)]="applyNo"
	#applyNoField="ngModel"
  />
</div>
```

```typescript
import { ViewChild } from '@angular/core';

@Component({
  selector: 'app-cyempty-booking-apply-query',
  imports: [RouterModule, FormsModule, BreadcrumbComponent, CommonModule],
  templateUrl: './cyempty-booking-apply-query.component.html',
  styleUrl: './cyempty-booking-apply-query.component.css',
})

export class CYEmptyBookingApplyQueryComponent{

  @ViewChild('applyNoField') inputField!: NgModel;
}
```

#### ViewChildren
> 存取多筆符合的HTML元素、component、樣板變數等等

[認識 ng-container](https://ithelp.ithome.com.tw/articles/10206099)

```html
<!-- 動態 BL No. 欄位 -->
<ng-container
*ngFor="let blNo of blNumbers; let i = index"
>
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
	  />
	</div>
  </div>
</div>
<div class="col-2 col-xs-1 col-lg-4"></div>
<div class="clearfix"></div>
</ng-container>
```

```typescript
import {QueryList,ViewChildren} from '@angular/core';
import { FormsModule, NgForm, NgModel } from '@angular/forms';

@Component({
  selector: 'app-check-release-pin-add',
  imports: [CommonModule, FormsModule, RouterLink],
  templateUrl: './check-release-pin-add.component.html',
  styleUrl: './check-release-pin-add.component.css',
})

export class CheckReleasePinAddComponent {
  // 使用 ViewChildren 獲取所有 blNoField
  @ViewChildren('blNoField') blNoFields!: QueryList<NgModel>;
}
```