
運用情境：
>會將多個欄位組成一個群組，然後會動態建立，就會以FormArray元素內包含多個FormGroup；而單一個formgroup就會將多個欄位設為一個群組

component：
```typescript
constructor(private fb: FormBuilder){
 this.searchDto = this.fb.array([]);
 this.applyForm = this.fb.group({ DataList: this.searchDto });
 
 this.addFormGroup(0);
}

applyForm: FormGroup;
searchDto: FormArray;

/**新增一櫃 */
  addFormGroup(index: number) {
    const dataListArray = this.applyForm.get('DataList') as FormArray;
    dataListArray.insert(
      index,
      this.fb.group({
        /**領櫃編號 */
        pickupOrderNo: this.fb.control('', [Validators.required]),

        /**櫃號 */
        cntrNo: this.fb.control('', [Validators.required]),

        /**Y:指定櫃號、N:代表櫃號 */
        [`nominatedYN_${index}`]: this.fb.control('Y', [Validators.required]),

        /**選擇日期 */
        estimatedDate: this.fb.control(''),

        /**選擇時段 */
        estimatedTime: this.fb.control(''),
      })
    );
  }
  
/**刪除一櫃 */
  reomveFormGroup(index: number) {
    const dataListArray = this.applyForm.get('DataList') as FormArray;
    if (index <= 0 || dataListArray.length <= 0) return;
    dataListArray.removeAt(index);
  }
  
get searchDtoList() {
    return (this.applyForm.get('DataList') as FormArray).controls;
  }
```

> 如上所述，applyForm(`FormGroup`)->DataList(`FormArray`)->多個`FormGroup`
> Html樣板要將(`FormArray`)裡面多個`FormGroup`元素設定雙向繫結，則需要依階層往下指定
> 
> 以下方Html樣板為例：
> `[formGroup]="applyForm"`->`formArrayName="DataList"`->`[formGroupName]="i"`
> 其中要繫結`FormArray`裡面多個`FormGroup`元素，則設定為`[formGroupName]="i"`

Html樣板：
```html
<form [formGroup]="applyForm">
                  <div class="row margin_0">
                    <div class="col-12" formArrayName="DataList">
                      <div
                        class="row"
                        *ngFor="let item of searchDtoList; let i = index"
                        [formGroupName]="i"
                      >
                        <label
                          class="col-4 col-xs-3 col-lg-2"
                          for="normalform-name"
                          >領櫃編號 與 櫃號 <small>*</small></label
                        >
                        <div class="col-6 col-xs-8 col-lg-6 form_upload_EW">
                          <div class="row">
                            <div class="col-12">
                              <input
                                type="text"
                                id="normalform-name"
                                name="normalform-name"
                                class="form_control_EW"
                                formControlName="pickupOrderNo"
                                placeholder="領櫃編號"
                              />
                            </div>
                          </div>
                          <div class="row">
                            <div class="col-12">
                              <input
                                type="text"
                                id="normalform-name"
                                name="normalform-name"
                                class="form_control_EW"
                                formControlName="cntrNo"
                                placeholder="櫃號"
                              />
                            </div>
                          </div>
                          <div class="row">
                            <div class="col-12">
                              <div>
                                <div class="md-radio">
                                  <input
                                    [id]="'nominatedY_' + i"
                                    type="radio"
                                    [name]="'nominatedYN_' + i"
                                    value="Y"
                                    [formControlName]="'nominatedYN_' + i"
                                  />
                                  <label [for]="'nominatedY_' + i"
                                    >指定櫃號</label
                                  >
                                </div>
                                <div class="md-radio">
                                  <input
                                    [id]="'nominatedN_' + i"
                                    type="radio"
                                    [name]="'nominatedYN_' + i"
                                    value="N"
                                    [formControlName]="'nominatedYN_' + i"
                                  />
                                  <label [for]="'nominatedN_' + i"
                                    >代表櫃號</label
                                  >
                                </div>
                              </div>
                            </div>
                          </div>
                          <div class="row" [id]="'SettingEstimatedDate_' + i">
                            <div class="col-12">
                              <select
                                class="form_control_EW"
                                formControlName="estimatedDate"
                              >
                                <option value="">選擇日期</option>
                                <option
                                  *ngFor="let date of DateRange"
                                  [value]="date"
                                >
                                  {{ date }}
                                </option>
                              </select>
                            </div>
                          </div>
                          <div class="row" [id]="'SettingEstimatedTime_' + i">
                            <div class="col-12">
                              <select
                                class="form_control_EW"
                                formControlName="estimatedTime"
                              >
                                <option value="">選擇時段</option>
                                <option
                                  *ngFor="let timePeriod of TimePeriodRange"
                                  [value]="timePeriod.codeValue"
                                >
                                  {{ timePeriod.codeName.substr(0, 5) }}
                                </option>
                              </select>
                            </div>
                          </div>
                          <div class="row">
                            <div class="col-12">
                              <button
                                type="button"
                                (click)="reomveFormGroup(i)"
                                *ngIf="canReomveFormGroup(i)"
                              >
                                <i class="fas fa-trash-alt"></i>刪除
                              </button>
                            </div>
                          </div>
                        </div>
                        <div class="col-2 col-xs-1 col-lg-4"></div>
                        <div class="clearfix"></div>
                      </div>
                      <label class="col-4 col-xs-3 col-lg-2"></label>
                      <div class="col-8 col-xs-8 col-lg-6 form_upload_EW">
                        <div class="row">
                          <div class="col-12">
                            <button
                              type="button"
                              (click)="addFormGroup(1)"
                              *ngIf="canAddFormGroup"
                            >
                              <i class="fas fa-plus"></i>新增一櫃
                            </button>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div class="clearfix"></div>
                  </div>
                </form>
```