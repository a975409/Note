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
  onFileChange(event: Event) {
     const input = event.target as HTMLInputElement;

     if (input.files && input.files.length > 0) {
       const file = input.files[0];

       this.electronicSealControl.setValue(file, { emitEvent: false });
     }
   }

  //點擊取消按鈕後清除選擇的檔案
  cancelFile() {
    this.electronicSealControl.setValue(null, { emitEvent: false });
    const inputFile = document.getElementById('ElectronicSeal') as HTMLInputElement;
     if (inputFile) {
       inputFile.value = ''; // 清除檔案選擇器的值
     }
  }
}
```

HTML樣板：
```html
<form [formGroup]="applyForm" (ngSubmit)="submitSignUp()">
	<i class="fas fa-paperclip"></i>上傳電子章
		<input type="file" name="ElectronicSeal" id="ElectronicSeal" (change)="onFileChange($event)" />
	<button *ngIf="electronicSealControl.value" (click)="cancelFile()">取消</button>
</form>
```
