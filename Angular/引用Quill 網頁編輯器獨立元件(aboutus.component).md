aboutus.component.ts：
```typescript
import { EditorComponent } from '../editor/editor.component';

@Component({
  imports: [EditorComponent],
})

export class AboutusComponent {}
```

aboutus.component.html：
```HTML
<app-editor></app-editor>
```
