HTML：
```html
<form #submitForm="ngForm">
  <table
	width="100%"
	cellpadding="0"
	cellspacing="0"
	class="table"
  >
	<tbody>
	  <tr
		*ngFor="
		let item of DisplayPayInfo.DataList;
		let bl_index = index;
		let isLast = last
	  "
	  >
		<td class="text-left">
		  <input
			type="text"
			[id]="'regNo_' + bl_index"
			[name]="'regNo_' + bl_index"
			placeholder="輸入統編"
			[(ngModel)]="item.regNo"
			(change)="regNoChange($event, bl_index)"
			#regNoField="ngModel"
		  />
		  <div
			*ngIf="
			  regNoField.invalid &&
			  (regNoField.dirty || regNoField.touched)
			"
			class="error-messages"
		  >
			<p
			  class="memo"
			  *ngIf="regNoField.errors?.['taxId']?.format"
			>
			  統編格式不正確
			</p>
			<p
			  class="memo"
			  *ngIf="regNoField.errors?.['taxId']?.validation"
			>
			  統編驗證失敗
			</p>
		  </div>
		</td>
	  </tr>
	</tbody>
  </table>
</form>
```

component：
```typescript
import { Component, OnInit, ViewChild } from '@angular/core';
import { FormsModule, NgForm } from '@angular/forms';

@Component({
  selector: 'app-empty-booking-pay-info-apply-submit',
  imports: [
    FormsModule,
  ],
  templateUrl: './empty-booking-pay-info-apply-submit.component.html',
  styleUrl: './empty-booking-pay-info-apply-submit.component.css',
})

export class EmptyBookingPayInfoApplySubmitComponent implements OnInit {
  @ViewChild('submitForm') submitForm!: NgForm;

/**設定已勾選的BLno統編驗證器 */

  setCheckedTaxIdValidator() {
    const checkedBLno: string[] = [];
    this.DisplayPayInfo.DataList.forEach((item: any, index: number) => {
      if (item.checked) checkedBLno.push('regNo_' + index);
    });
	
    setMultiTaxIdValidatorForNgForm(this.submitForm, checkedBLno);
  }

/**NgForm底下多個NgModel設定統編驗證器 */
	setMultiTaxIdValidatorForNgForm(submitForm: NgForm,KeyElement: string[]) {
	  setTimeout(() => {
	    if (submitForm && submitForm.controls) {
	      Object.keys(submitForm.controls).forEach((key) => {
	        
	        //key：HTML name屬性值
	        const control = submitForm.controls[key];

			//以下就是設定自定的驗證器
	        control.clearValidators();
	        if (KeyElement.includes(key)) {
	          control.addValidators(taxIdValidator());
	        }
	
	        control.updateValueAndValidity();
	      });
	    }
	  }, 0);
	}
}
```

