1. 安裝套件：
```text
# 安裝 Quill 核心套件
npm install quill --save

# 安裝 ngx-quill (Angular 的 Quill 整合套件) (Angular 19需安裝此版本)
npm i ngx-quill@27.1.2

# 安裝 quill-table 模組 (用於表格功能)
npm install quill-table --save
```

2. 設定angular.json
```json
"build": {
  "options": {
	"styles": [
	  //引用以下3個css檔案
	  "./node_modules/quill/dist/quill.core.css",
	  "./node_modules/quill/dist/quill.snow.css",
	  "./node_modules/quill/dist/quill.bubble.css"
	],
	"scripts": [
		//不用設定
	]
  }
},
```

3. 全局樣式設定
```css
/* 確保表格在編輯器中顯示正確 */
.ql-editor table {
  border-collapse: collapse;
  width: 100%;
  margin-bottom: 10px;
}

.ql-editor table td,
.ql-editor table th {
  border: 1px solid #ddd;
  padding: 8px;
}

.ql-editor table th {
  background-color: #f2f2f2;
  font-weight: bold;
}

.ql-editor tr:nth-child(even) {
  background-color: #f9f9f9;
}

/* 自定義表格按鈕樣式 */
.ql-table {
  background: none;
  border: none;
  cursor: pointer;
  height: 24px;
  padding: 3px 5px;
  width: 28px;
}

.ql-table svg {
  height: 18px;
  width: 18px;
}
```

4. quill-init.service.ts：
```typescript
// src/app/services/quill-init.service.ts
import { Injectable } from '@angular/core';
import Quill from 'quill';
import { QuillTableService } from './quill-table.service';

@Injectable({
  providedIn: 'root',
})
export class QuillInitService {
  constructor(private quillTableService: QuillTableService) {
    this.initializeQuill();
  }

  initializeQuill() {
    try {
      // 初始化表格支援
      this.quillTableService.initializeTableSupport();

      // 註冊自定義表格按鈕處理器
      const tableHandler = {
        table: () => {
          // 這裡不需要實際的處理邏輯，因為我們會在組件中處理
          console.log('Table button clicked');
        },
      };

      // 將表格按鈕處理器添加到 Quill 中
      if (Quill) {
        // 創建自定義表格模組
        const tableModule = {
          handlers: tableHandler,
        };

        // 註冊自定義模組
        Quill.register('modules/table', () => tableModule, true);

        console.log('Quill table module registered');
      }
    } catch (error) {
      console.error('Error initializing Quill:', error);
    }
  }
}
```

5. quill-table.service.ts：
```typescript
// src/app/services/quill-table.service.ts
import { Injectable } from '@angular/core';
import Quill from 'quill';

@Injectable({
  providedIn: 'root',
})
export class QuillTableService {
  initializeTableSupport() {
    // 確保 Quill 已經載入
    if (typeof Quill === 'undefined') {
      console.error('Quill is not loaded');
      return;
    }

    try {
      // 正確引入 Blot 類別，使用明確的類型定義
      const Block = Quill.import('blots/block') as any;
      const Container = Quill.import('blots/container') as any;

      // 確保 Block 和 Container 是可用的
      if (!Block || !Container) {
        console.error('Quill blots not available');
        return;
      }

      // 表格單元格
      class TableCellBlot extends Block {
        static blotName = 'table-cell';
        static tagName = 'TD';

        static create(value: any) {
          const node = super.create();
          node.setAttribute('style', 'border: 1px solid #ddd; padding: 8px;');
          return node;
        }
      }

      // 表格行
      class TableRowBlot extends Container {
        static blotName = 'table-row';
        static tagName = 'TR';
      }

      // 表格
      class TableBlot extends Container {
        static blotName = 'table';
        static tagName = 'TABLE';

        static create() {
          const node = super.create();
          node.setAttribute(
            'style',
            'width: 100%; border-collapse: collapse; margin-bottom: 10px;'
          );
          return node;
        }
      }

      // 表格頭單元格
      class TableHeaderCellBlot extends Block {
        static blotName = 'table-header-cell';
        static tagName = 'TH';

        static create(value: any) {
          const node = super.create();
          node.setAttribute(
            'style',
            'border: 1px solid #ddd; padding: 8px; background-color: #f2f2f2; font-weight: bold;'
          );
          return node;
        }
      }

      // 表格主體
      class TableBodyBlot extends Container {
        static blotName = 'table-body';
        static tagName = 'TBODY';
      }

      // 表格頭
      class TableHeaderBlot extends Container {
        static blotName = 'table-header';
        static tagName = 'THEAD';
      }

      // 註冊所有表格相關的 Blots
      Quill.register(TableCellBlot);
      Quill.register(TableRowBlot);
      Quill.register(TableBlot);
      Quill.register(TableHeaderCellBlot);
      Quill.register(TableBodyBlot);
      Quill.register(TableHeaderBlot);

      console.log('Quill table support initialized');
    } catch (error) {
      console.error('Error initializing Quill table support:', error);
    }
  }

  // 插入表格的方法
  insertTable(quill: any, rows: number = 3, cols: number = 3) {
    if (!quill) return;

    // 獲取當前選擇位置
    const range = quill.getSelection(true);

    // 創建表格 HTML
    let tableHTML =
      '<table style="width:100%; border-collapse:collapse; margin-bottom:10px;">';

    // 創建表頭
    tableHTML += '<thead><tr>';
    for (let i = 0; i < cols; i++) {
      tableHTML +=
        '<td style="border:1px solid #ddd; padding:8px; background-color:#f2f2f2;">標題 ' +
        (i + 1) +
        '</td>';
    }
    tableHTML += '</tr></thead>';

    // 創建表格內容
    tableHTML += '<tbody>';
    for (let i = 0; i < rows - 1; i++) {
      tableHTML += '<tr>';
      for (let j = 0; j < cols; j++) {
        tableHTML +=
          '<td style="border:1px solid #ddd; padding:8px;">單元格 ' +
          (i + 1) +
          '-' +
          (j + 1) +
          '</td>';
      }
      tableHTML += '</tr>';
    }
    tableHTML += '</tbody></table><p><br></p>';

    // 在當前位置插入表格
    quill.clipboard.dangerouslyPasteHTML(range.index, tableHTML, 'user');
  }

  // 為表格添加事件監聽和操作功能
  addTableOperations(quill: any) {
    // 為表格添加點擊事件處理
    const editor = quill.root;

    editor.addEventListener('click', (event: MouseEvent) => {
      const target = event.target as HTMLElement;

      // 檢查是否點擊了表格單元格
      if (target.tagName === 'TD' || target.tagName === 'TH') {
        // 高亮顯示選中的單元格
        this.highlightCell(target);
      }
    });
  }

  // 高亮顯示選中的單元格
  private highlightCell(cell: HTMLElement) {
    // 移除所有單元格的高亮
    const allCells = document.querySelectorAll('.ql-editor td, .ql-editor th');
    allCells.forEach((el) => {
      (el as HTMLElement).style.outline = 'none';
    });

    // 高亮當前單元格
    cell.style.outline = '2px solid #2196f3';
  }

  // 刪除選中的行
  deleteRow(quill: any) {
    const selectedCell = document.querySelector(
      '.ql-editor td[style*="outline"], .ql-editor th[style*="outline"]'
    ) as HTMLElement;
    if (selectedCell) {
      const row = selectedCell.closest('tr');
      if (row) {
        row.remove();
      }
    }
  }

  // 刪除選中的列
  deleteColumn(quill: any) {
    const selectedCell = document.querySelector(
      '.ql-editor td[style*="outline"], .ql-editor th[style*="outline"]'
    ) as HTMLElement;
    if (selectedCell) {
      const table = selectedCell.closest('table');
      if (table) {
        const cellIndex = Array.from(
          selectedCell.parentElement!.children
        ).indexOf(selectedCell);
        const rows = table.querySelectorAll('tr');

        rows.forEach((row) => {
          if (row.children[cellIndex]) {
            row.children[cellIndex].remove();
          }
        });
      }
    }
  }

  // 在選中的單元格上方插入行
  insertRowAbove(quill: any) {
    const selectedCell = document.querySelector(
      '.ql-editor td[style*="outline"], .ql-editor th[style*="outline"]'
    ) as HTMLElement;
    if (selectedCell) {
      const row = selectedCell.closest('tr');
      if (row) {
        const newRow = document.createElement('tr');
        const cellCount = row.children.length;

        for (let i = 0; i < cellCount; i++) {
          const cell = document.createElement('td');
          cell.style.border = '1px solid #ddd';
          cell.style.padding = '8px';
          cell.innerHTML = '&nbsp;';
          newRow.appendChild(cell);
        }

        row.parentNode!.insertBefore(newRow, row);
      }
    }
  }

  // 在選中的單元格下方插入行
  insertRowBelow(quill: any) {
    const selectedCell = document.querySelector(
      '.ql-editor td[style*="outline"], .ql-editor th[style*="outline"]'
    ) as HTMLElement;
    if (selectedCell) {
      const row = selectedCell.closest('tr');
      if (row) {
        const newRow = document.createElement('tr');
        const cellCount = row.children.length;

        for (let i = 0; i < cellCount; i++) {
          const cell = document.createElement('td');
          cell.style.border = '1px solid #ddd';
          cell.style.padding = '8px';
          cell.innerHTML = '&nbsp;';
          newRow.appendChild(cell);
        }

        if (row.nextSibling) {
          row.parentNode!.insertBefore(newRow, row.nextSibling);
        } else {
          row.parentNode!.appendChild(newRow);
        }
      }
    }
  }
}
```

6. app.config.ts設定如下：
```typescript
import {importProvidersFrom} from '@angular/core';
import { QuillModule } from 'ngx-quill';
import { QuillInitService } from './services/quill-init.service';

export const appConfig: ApplicationConfig = {
  providers: [
    importProvidersFrom(
      QuillModule.forRoot({
        modules: {
          toolbar: [
            ['bold', 'italic', 'underline', 'strike'],
            ['blockquote', 'code-block'],
            [{ header: 1 }, { header: 2 }],
            [{ list: 'ordered' }, { list: 'bullet' }],
            [{ script: 'sub' }, { script: 'super' }],
            [{ indent: '-1' }, { indent: '+1' }],
            [{ direction: 'rtl' }],
            [{ size: ['small', false, 'large', 'huge'] }],
            [{ header: [1, 2, 3, 4, 5, 6, false] }],
            [{ color: [] }, { background: [] }],
            [{ font: [] }],
            [{ align: [] }],
            ['clean'],
            ['link', 'image', 'video'],
            ['table'], // 添加表格按鈕
          ],
          table: true,
        },
      })
    ),
    QuillInitService,
  ],
};
```

7. [[建立Quill 網頁編輯器獨立元件(editor.component)]]
8. [[引用Quill 網頁編輯器獨立元件(aboutus.component)]]