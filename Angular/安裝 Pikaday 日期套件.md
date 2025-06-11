執行以下指令：
```
npm install pikaday
```

安裝typescrpit描述檔：
```
npm install @types/pikaday --save-dev
```

在`angular.json`設定jQuery套件的路徑：
```json
"build": {
          "builder": "@angular-devkit/build-angular:application",
          "options": {
            "outputPath": "dist/system-wanhai-frontend",
            "index": "src/index.html",
            "browser": "src/main.ts",
            "polyfills": ["zone.js"],
            "tsConfig": "tsconfig.app.json",
            "assets": ["src/assets"],
            "styles": ["./node_modules/pikaday/css/pikaday.css"],
            "scripts": [
              "./node_modules/pikaday/pikaday.js"
            ]
          },
        },
```

> 通常在開發的時候，執行的環境是`build`，所以只要設定`build`就好

承上點，在`tsConfig`指定的檔名內的`types`陣列內新增`jQuery`
```json
/* To learn more about Typescript configuration file: https://www.typescriptlang.org/docs/handbook/tsconfig-json.html. */
/* To learn more about Angular compiler options: https://angular.dev/reference/configs/angular-compiler-options. */
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app",
    "types": ["pikaday"]
  },
  "files": [
    "src/main.ts"
  ],
  "include": [
    "src/**/*.d.ts"
  ]
}

```

建立`PikadayDirective`：
```typescript
// pikaday.directive.ts
import {
  Directive,
  ElementRef,
  EventEmitter,
  HostListener,
  Input,
  OnDestroy,
  OnInit,
  Output,
} from '@angular/core';
import Pikaday from 'pikaday';

@Directive({
  selector: '[appPikaday]',
  standalone: true,
})
export class PikadayDirective implements OnInit, OnDestroy {
  @Input() dateValue: Date | string | null = null;
  @Output() dateChange = new EventEmitter<Date | null>();
  @Input() minDate: Date | null = null;

  private picker: Pikaday | null = null;
  private isValidDate: boolean = true;

  constructor(private elementRef: ElementRef) {}

  ngOnInit(): void {
    const options: any = {
      field: this.elementRef.nativeElement,
      format: 'YYYY/MM/DD',
      onSelect: (date: Date) => {
        this.isValidDate = true;
        this.dateChange.emit(date);
      },
    };

    if (this.minDate) {
      options.minDate = this.minDate;
    }

    this.picker = new Pikaday(options);

    // 如果有初始值，設置日期
    if (this.dateValue) {
      try {
        const date = new Date(this.dateValue);
        if (!isNaN(date.getTime())) {
          this.picker.setDate(date);
          this.isValidDate = true;
        } else {
          this.clearInput();
        }
      } catch (e) {
        this.clearInput();
      }
    }
  }

  // 監聽輸入事件
  @HostListener('input', ['$event'])
  onInput(event: Event): void {
    const inputElement = event.target as HTMLInputElement;
    const value = inputElement.value;

    if (value) {
      // 檢查輸入的值是否為有效日期
      const isValid = this.isValidDateFormat(value);

      if (!isValid) {
        this.isValidDate = false;
      } else {
        this.isValidDate = true;
      }
    }
  }

  // 監聽失焦事件
  @HostListener('blur')
  onBlur(): void {
    const value = this.elementRef.nativeElement.value;

    if (value && !this.isValidDate) {
      this.clearInput();
    }
  }

  private isValidDateFormat(dateString: string): boolean {
    // 檢查日期格式 (YYYY/MM/DD)
    const regex = /^\d{4}\/\d{2}\/\d{2}$/;
    if (!regex.test(dateString)) return false;

    // 進一步檢查日期是否有效
    const parts = dateString.split('/');
    const year = parseInt(parts[0], 10);
    const month = parseInt(parts[1], 10) - 1; // 月份從 0 開始
    const day = parseInt(parts[2], 10);

    const date = new Date(year, month, day);

    return (
      date.getFullYear() === year &&
      date.getMonth() === month &&
      date.getDate() === day
    );
  }

  private clearInput(): void {
    this.elementRef.nativeElement.value = '';
    this.dateChange.emit(null);
  }

  ngOnDestroy(): void {
    if (this.picker) {
      this.picker.destroy();
    }
  }
}

```

在component 引用`PikadayDirective`：
```typescript
import { PikadayDirective } from '../../directives/pikaday.directive';

@Component({
  selector: 'app-date-picker',
  imports: [
    PikadayDirective,
  ],
  template: `<input type="text" class="form-control" id="datepicker">`
})
export class DatePickerComponent {
  
}
```

HTML：
```html
<input
  *ngIf="canChecked(item)"
  type="text"
  [id]="setEstPickupdateId(i)"
  appPikaday
  [dateValue]="item.estRtndate"
  [minDate]="startDate"
  (dateChange)="updateDate(i, $event)"
  placeholder="預計還櫃日"
  [attr.aria-payInfoBLno-index]="i"
/>
```

> dateValue、minDate、dateChange為`PikadayDirective`的參數，appPikaday為`PikadayDirective`的selector
