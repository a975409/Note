 > 假設`formgroup`內，需依據兩個輸入欄位的值進行驗證，則
 
 驗證器：
```typescript
export function CYFullPickupDataApplyQueryValidator(
  unixTimeFormateService: UnixTimeFormateService
): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    //control 為formgroup，get的輸入參數為輸入欄位
    const estimatedDate = control.get('estimatedDate');
    const estimatedTime = control.get('estimatedTime');

    if (!estimatedDate || !estimatedTime) {
      return null;
    }

    var now = Date.now();

    try {
      const selectUxitDateTime =
        unixTimeFormateService.taipeiDateTimeToUnixTime(
          `${estimatedDate?.value} ${estimatedTime?.value}:00`
        );

      return now < selectUxitDateTime
        ? {
            outOfRange: true,
          }
        : null;
    } catch (error) {
      return null;
    }
  };
}
```

`FromGroup`套用驗證器的方式：
```typescript
this.fb.group(
	{
	  /**領櫃編號 */
	  pickupOrderNo: this.fb.control('', [Validators.required]),

	  /**櫃號 */
	  cntrNo: this.fb.control('', [Validators.required, ctnrNoValidator()]),

	  /**Y:指定櫃號、N:代表櫃號 */
	  [`nominatedYN_${index}`]: this.fb.control('Y', [Validators.required]),

	  /**選擇日期 */
	  //estimatedDate: this.fb.control('', [Validators.required]),
	  estimatedDate: this.fb.control(''),

	  /**選擇時段 */
	  //estimatedTime: this.fb.control('', [Validators.required]),
	  estimatedTime: this.fb.control(''),
	},
	{
	  //套用驗證器
	  validators: CYFullPickupDataApplyQueryValidator(
		this.unixTimeFormateService
	  ),
	}
  )
```

HTML：
```html
<!-- 錯誤訊息顯示 -->
<!-- item為formgroup，outOfRange為驗證器所回傳的驗證結果-->
<div
*ngIf="item.touched || item.dirty"
class="error-messages"
>
<p
  class="memo"
  *ngIf="item.errors?.['outOfRange']"
>
  無法選擇超過當下時間的選項
</p>
</div>
```