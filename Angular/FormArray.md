[FormArray](https://angular.tw/api/forms/FormArray)
[[FormArray元素為FormControl]]
[[FormArray元素為FormGroup]]

新增元素：
```typescript
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
```

刪除指定元素：
```typescript
 /**取得多筆已輸入的車牌欄位 */
  get carNoArray() {
    return this.applyForm.get('CarNolist') as FormArray;
  }

  /**移除特定的車牌欄位 */
  removeCarNo(index: number) {
    this.carNoArray.removeAt(index);
  }
```

讀取FormArray陣列元素：
```typescript
get carNoArray() {
    return this.applyForm.get('CarNolist') as FormArray;
  }

carNoArray.controls.forEach((item,index)=>{})

//或是從formgroup取得CarNolist的值，會直接取得陣列資料
this.applyForm.value.CarNolist
```
