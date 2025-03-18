> 在 `ngOnChanges()` 中，`changes['currentPage']` 讀取到的是**輸入屬性的原始變更資訊**，而不是透過 getter 方法取得的值。

## ngOnChanges 與 Getter/Setter 的關係

當你在 Angular 元件中使用 `@Input()` 裝飾器並結合 getter/setter 時，發生的順序和行為如下：

1. 當父元件傳遞值到子元件的 `currentPage` 屬性時，Angular 首先觸發 `ngOnChanges()` 方法
2. 在 `ngOnChanges()` 中，`changes['currentPage']` 包含的是**變更前後的原始值**，而不是經過 setter 處理後的值
3. 在 `ngOnChanges()` 執行**之後**，Angular 才會調用 `set currentPage()` setter 方法

## changes['currentPage'] 的內容

在 `ngOnChanges()` 中，`changes['currentPage']` 是一個 `SimpleChange` 物件，它包含以下屬性：

- `currentValue`: 新的輸入值（父元件傳遞的原始值）
- `previousValue`: 舊的輸入值
- `firstChange`: 布林值，表示這是否是第一次變更

這些值都是**直接從 Angular 的變更檢測機制**取得的，完全不會經過你定義的 getter 或 setter 方法。

## 範例說明

```typescript
import { Component, Input, OnChanges, SimpleChanges } from '@angular/core';

@Component({
  selector: 'app-example',
  template: '<div>{{ displayValue }}</div>'
})
export class ExampleComponent implements OnChanges {
  displayValue: string = '';
  
  private _currentPage = 1;

  @Input()
  set currentPage(value: number) {
    console.log('3. setter 被調用，value =', value);
    this._currentPage = value * 2; // 故意修改值，以展示差異
    console.log('   _currentPage 被設為', this._currentPage);
  }
  
  get currentPage(): number {
    console.log('getter 被調用');
    return this._currentPage;
  }

  ngOnChanges(changes: SimpleChanges) {
    if (changes['currentPage']) {
      console.log('1. ngOnChanges 檢測到 currentPage 變更');
      console.log('2. changes[\'currentPage\'].currentValue =', changes['currentPage'].currentValue);
      console.log('   changes[\'currentPage\'].previousValue =', changes['currentPage'].previousValue);
      console.log('   changes[\'currentPage\'].firstChange =', changes['currentPage'].firstChange);
      console.log('   此時 this._currentPage =', this._currentPage);
      
      this.displayValue = `頁碼從 ${changes['currentPage'].previousValue} 變更為 ${changes['currentPage'].currentValue}`;
    }
  }
}
```

如果父元件將 `currentPage` 從 1 變更為 5，控制台輸出會類似於：

```
1. ngOnChanges 檢測到 currentPage 變更
2. changes['currentPage'].currentValue = 5
   changes['currentPage'].previousValue = 1
   changes['currentPage'].firstChange = false
   此時 this._currentPage = 1
3. setter 被調用，value = 5
   _currentPage 被設為 10
```

## 重要結論

1. `ngOnChanges()` 先於 setter 方法執行
2. `changes['currentPage'].currentValue` 是原始的輸入值，不受 getter/setter 影響
3. 在 `ngOnChanges()` 執行期間，`this._currentPage` 仍然保持舊值，因為 setter 尚未被調用
4. 如果你在 `ngOnChanges()` 中呼叫 `this.currentPage`，這時才會調用 getter 方法

這種行為是 Angular 變更檢測機制的設計，確保你能在 `ngOnChanges()` 中獲取到原始的變更資訊，無論你是否定義了自訂的 getter/setter。