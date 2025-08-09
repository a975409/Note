
editor.component.ts：
```typescript
import { CommonModule } from '@angular/common';
import { Component, OnInit, signal } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { DomSanitizer, SafeHtml } from '@angular/platform-browser';
import { QuillModule } from 'ngx-quill';
import { QuillTableService } from '../../services/quill-table.service';

@Component({
  selector: 'app-editor',
  imports: [FormsModule, CommonModule, QuillModule],
  templateUrl: './editor.component.html',
  styleUrl: './editor.component.css',
})
export class EditorComponent implements OnInit {
  editorContent = signal('');
  sanitizedContent = signal<SafeHtml>('');
  showTableDialog = signal(false);
  showPreview = signal(false);
  isTableSelected = signal(false);
  quillEditor: any;
  tableRows = 3;
  tableCols = 3;

  constructor(
    private quillTableService: QuillTableService,
    private sanitizer: DomSanitizer
  ) {}

  ngOnInit() {}

  updateContent(content: string) {
    this.editorContent.set(content);
    this.sanitizedContent.set(this.sanitizer.bypassSecurityTrustHtml(content));
  }

  togglePreview() {
    // 清除所有單元格的選取樣式
    this.clearTableCellSelection();
    this.showPreview.set(true);
  }

  closePreview() {
    this.showPreview.set(false);
  }

  // 添加清除表格單元格選取樣式的方法
  clearTableCellSelection() {
    // 創建一個臨時的 div 元素來處理 HTML 內容
    const tempDiv = document.createElement('div');
    tempDiv.innerHTML = this.editorContent();

    // 移除所有表格單元格的 outline 樣式
    const cells = tempDiv.querySelectorAll('td, th');
    cells.forEach((cell) => {
      (cell as HTMLElement).style.outline = 'none';
    });

    // 更新預覽內容，但不更新編輯器內容
    this.sanitizedContent.set(
      this.sanitizer.bypassSecurityTrustHtml(tempDiv.innerHTML)
    );
  }

  onEditorCreated(editor: any) {
    this.quillEditor = editor;

    // 監聽工具欄中的表格按鈕點擊事件
    const toolbar = editor.getModule('toolbar');
    if (toolbar) {
      toolbar.addHandler('table', () => {
        this.showTableDialog.set(true);
      });
    }

    // 添加表格操作功能
    this.quillTableService.addTableOperations(editor);

    // 監聽編輯器點擊事件，檢查是否選中了表格單元格
    editor.root.addEventListener('click', (event: MouseEvent) => {
      const target = event.target as HTMLElement;
      this.isTableSelected.set(
        target.tagName === 'TD' || target.tagName === 'TH'
      );
    });
  }

  insertTable() {
    this.showTableDialog.set(true);
  }

  confirmInsertTable() {
    if (this.quillEditor) {
      this.quillTableService.insertTable(
        this.quillEditor,
        this.tableRows,
        this.tableCols
      );
      this.showTableDialog.set(false);

      // 更新預覽內容
      this.updateContent(this.quillEditor.root.innerHTML);
    }
  }

  cancelInsertTable() {
    this.showTableDialog.set(false);
  }

  // 表格操作方法
  insertRowAbove() {
    this.quillTableService.insertRowAbove(this.quillEditor);
    this.updateContent(this.quillEditor.root.innerHTML);
  }

  insertRowBelow() {
    this.quillTableService.insertRowBelow(this.quillEditor);
    this.updateContent(this.quillEditor.root.innerHTML);
  }

  deleteRow() {
    this.quillTableService.deleteRow(this.quillEditor);
    this.updateContent(this.quillEditor.root.innerHTML);
  }

  deleteColumn() {
    this.quillTableService.deleteColumn(this.quillEditor);
    this.updateContent(this.quillEditor.root.innerHTML);
  }
}
```

editor.component.html：
```html
<div class="editor-container">
  <h2>Quill 編輯器 (含表格功能)</h2>

  <div class="toolbar-actions">
    <button (click)="insertTable()" class="action-btn">插入表格</button>
    <ng-container *ngIf="isTableSelected()">
      <button (click)="insertRowAbove()" class="action-btn">上方插入行</button>
      <button (click)="insertRowBelow()" class="action-btn">下方插入行</button>
      <button (click)="deleteRow()" class="action-btn">刪除行</button>
      <button (click)="deleteColumn()" class="action-btn">刪除列</button>
    </ng-container>
    <button (click)="togglePreview()" class="preview-btn">預覽</button>
  </div>

  <quill-editor
    [ngModel]="editorContent()"
    (ngModelChange)="updateContent($event)"
    [styles]="{ height: '400px' }"
    (onEditorCreated)="onEditorCreated($event)"
  ></quill-editor>

  <div class="table-size-dialog" *ngIf="showTableDialog()">
    <div class="dialog-content">
      <h3>插入表格</h3>
      <div class="form-group">
        <label>行數:</label>
        <input type="number" min="1" max="10" [(ngModel)]="tableRows" />
      </div>
      <div class="form-group">
        <label>列數:</label>
        <input type="number" min="1" max="10" [(ngModel)]="tableCols" />
      </div>
      <div class="dialog-actions">
        <button (click)="confirmInsertTable()" class="btn-primary">插入</button>
        <button (click)="cancelInsertTable()" class="btn-secondary">
          取消
        </button>
      </div>
    </div>
  </div>

  <div class="preview-dialog" *ngIf="showPreview()">
    <div class="preview-dialog-content">
      <div class="preview-header">
        <h3>內容預覽</h3>
        <button (click)="closePreview()" class="close-btn">&times;</button>
      </div>
      <div class="preview-body" [innerHTML]="sanitizedContent()"></div>
    </div>
  </div>
</div>
```