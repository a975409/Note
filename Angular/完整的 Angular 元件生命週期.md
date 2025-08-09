第一次載入元件：
![[Pasted image 20250317003113.png]]

後續更新元件：
![[Pasted image 20250317003146.png]]

1. `constructor()`
2. [[ngOnChanges()]] - 當輸入屬性變化時(此時原有輸入屬性值尚未更新，但可存取舊的輸入值和新的輸入值)
3. `ngOnInit()` - 只執行一次，在第一次 `ngOnChanges()` 之後
4. `ngDoCheck()` - 每次變化檢測時
5. `ngAfterContentInit()` - 在元件內容初始化後
6. `ngAfterContentChecked()` - 在元件內容檢查後
7. `ngAfterViewInit()` - 在元件視圖初始化後
8. `ngAfterViewChecked()` - 在元件視圖檢查後
9. `ngOnDestroy()` - 在元件銷毀前

最新版angular生命週期順序：
https://angular.dev/guide/components/lifecycle#execution-order

[淺談 Angular 元件生命週期](https://hackmd.io/@Heidi-Liu/angular-lifecycle)
[元件生命週期(V16)](https://angular.tw/guide/lifecycle-hooks)
