component：
```typescript
import { CommonModule } from '@angular/common';
import { Component } from '@angular/core';
import {
  FormBuilder,
  FormControl,
  FormGroup,
  ReactiveFormsModule,
} from '@angular/forms';

@Component({
  selector: 'app-signup',
  imports: [CommonModule, ReactiveFormsModule],
  templateUrl: './signup.component.html',
  styleUrl: './signup.component.css',
})
export class SignupComponent {
  applyForm: FormGroup;
  ElectronicSeal: FormControl;
  selectedFileName: string = '未選擇任何檔案';
  
  constructor(private fb: FormBuilder) {
    this.ElectronicSeal = new FormControl<File | null>(null);
    this.applyForm = this.fb.group({
      ElectronicSeal: this.ElectronicSeal,
    });
  }

  /**送出註冊資料 */
  submitSignUp() {
    let submitFormData = {};
  }

  get electronicSealControl() {
    return this.applyForm.get('ElectronicSeal') as FormControl;
  }

  // 處理檔案上傳
  onFileSelect(): void {
    // 建立隱藏的檔案輸入元素
    const fileInput = document.createElement('input');
    fileInput.type = 'file';

    // 監聽檔案選擇事件
    fileInput.onchange = (event: any) => {
      const file = event.target.files[0];
      if (file) {
        // 設定檔案到 FormControl
        this.electronicSealControl.setValue(file, { emitEvent: false });
        // 更新顯示的檔案名稱
        this.selectedFileName = file.name;
      }
    };

    // 觸發檔案選擇視窗
    fileInput.click();
  }

  //點擊取消按鈕後清除選擇的檔案
  cancelFile() {
    this.electronicSealControl.setValue(null, { emitEvent: false });
    this.selectedFileName = '';
  }
}
```

HTML樣板：
```html
<form [formGroup]="applyForm" (ngSubmit)="submitSignUp()">
	<button type="button" (click)="onFileSelect()">
	<i class="fas fa-paperclip"></i>上傳電子章</button>
	<span>{{ selectedFileName }}</span>
	<button *ngIf="electronicSealControl.value" (click)="cancelFile()">取消</button>
</form>
```
