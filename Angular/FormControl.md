
https://angular.tw/api/forms/FormControl

設定`FormControl`的設定值：
```typescript
updatefavoriteColor() {
  this.favoriteColorControl:FormControl;
  this.favoriteColorControl.setValue('Nancy');
}
```

取得`FormControl`的設定值：
```typescript
this.favoriteColorControl:FormControl;
this.favoriteColorControl.value ?? '';

<!--或從formgroup.value取得設定值-->
this.applyForm = this.fb.group({
	favoriteColorControl: this.favoriteColorControl,
});
    
this.applyForm.value.favoriteColorControl ?? '';
```

> FormControl 可能會回傳 null。如果值為 null，此程式碼使用空值合併運算符 ?? 將其預設為空字串。

`FormControl` 訂閱值變更事件：
```typescript
let active = this.applyForm.get('active') as FormControl;
    //emitEvent: false=>代表設定值之後，不要觸發任何事件
	active.setValue(DataList.locked == 0, { emitEvent: false });

    //訂閱值變更事件
    active.valueChanges.subscribe((value) => {
      if (this.canUpdateUserInfo()) this.lockedChange(value);
      else active.setValue(DataList.locked == 0, { emitEvent: false });
    });
```

從`FormGroup`下取得`FormControl` 或 `FormArray`：
```typescript
this.applyForm:FormGroup;

this.applyForm = this.fb.group({
      CarNo: this.fb.array<FormControl>([]),
      ElectronicSeal: this.fb.control<File | null>(null),
    });

let electronicSealControl = this.applyForm.get('ElectronicSeal') as FormControl;
let CarNo = this.applyForm.get('CarNo') as FormArray;
```

