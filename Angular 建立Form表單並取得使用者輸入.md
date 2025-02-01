
```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { HousingService } from '../housing.service';

//新增以下程式碼
import { FormControl, FormGroup, ReactiveFormsModule } from '@angular/forms';

@Component({
  selector: 'app-details',
  
  //需引入ReactiveFormsModule
  imports: [CommonModule, ReactiveFormsModule],
  template: `
  <article>
    <section class="listing-apply">
      <h2 class="section-heading">Apply now to live here</h2>
      <form [formGroup]="applyForm" (submit)="submitApplication()">
        <label for="first-name">First Name</label>
        <input id="first-name" type="text" formControlName="firstName">

        <label for="last-name">Last Name</label>
        <input id="last-name" type="text" formControlName="lastName">

        <label for="email">Email</label>
        <input id="email" type="email" formControlName="email">
        <button type="submit" class="primary">Apply now</button>
      </form>
    </section>
  </article>
`,
  styleUrls: ['./details.component.css'],
})
export class DetailsComponent {
  constructor(private housingService: HousingService) {}

  //建立表單物件
  applyForm = new FormGroup({
    firstName: new FormControl(''),
    lastName: new FormControl(''),
    email: new FormControl(''),
  });

  //this.applyForm.value.=>取得input輸入的值
  submitApplication() {
      this.housingService.submitApplication(
      this.applyForm.value.firstName ?? '',
      this.applyForm.value.lastName ?? '',
      this.applyForm.value.email ?? ''
    );
  }
}
```

> FormGroup代表一個完整的Form表單，FormControl則是Form表單下的HTML input輸入。FormControl 型別可以提供預設值並預設表單資料的形態。在此範例中，firstName 是一個 string，預設值為空字串。

> FormControl 可能會回傳 null。如果值為 null，此程式碼使用空值合併運算符 ?? 將其預設為空字串。
