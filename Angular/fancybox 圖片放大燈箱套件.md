安裝指令：
```
npm i fancybox
```

設定引用套件`angular.json`
```json
"build": {
          "builder": "@angular-devkit/build-angular:application",
          "options": {
            "outputPath": "dist/system-wanhai-frontend",
            "index": "src/index.php",
            "browser": "src/main.ts",
            "polyfills": ["zone.js"],
            "tsConfig": "tsconfig.app.json",
            "assets": ["src/assets"],
            "styles": [
              "@fancyapps/ui/dist/fancybox/fancybox.css"
            ],
            "scripts": [
              "@fancyapps/ui/dist/fancybox/fancybox.umd.js"
            ]
          }
        },
```

component初始設定：
```typescript
import {
  ElementRef,
  OnDestroy,
  OnInit,
} from '@angular/core';

@Component({
  selector: 'app-gallery',
  templateUrl: './gallery.component.html',
  styleUrls: ['./gallery.component.css'],
})
export class GalleryComponent {
  constructor(private elRef: ElementRef) {}

  ngOnInit() {
    //要綁定[data-fancybox]屬性
    Fancybox.bind(this.elRef.nativeElement, '[data-fancybox]', {
      // Custom options
      Carousel: {
        Thumbs: {
          type: 'classic',
        },
      },
    });
  }

  ngOnDestroy() {
    Fancybox.unbind(this.elRef.nativeElement);
    Fancybox.close();
  }
}
```

Html：
```html
<div class="photo_area_EW">
	<a
	  [attr.data-fancybox]="'gallery' + i"
	  href="assets/images/img_photo.jpg"
	>
	  <img
		src="assets/images/img_photo.jpg"
		class="img-responsive"
		alt=""
	  />
	</a>
	<a
	  [attr.data-fancybox]="'gallery' + i"
	  href="assets/images/img_photo.jpg"
	>
	  <img
		src="assets/images/img_photo.jpg"
		class="img-responsive"
		alt=""
	  />
	</a>
	<a
	  [attr.data-fancybox]="'gallery' + i"
	  href="assets/images/img_photo.jpg"
	>
	  <img
		src="assets/images/img_photo.jpg"
		class="img-responsive"
		alt=""
	  />
	</a>
	<a
	  [attr.data-fancybox]="'gallery' + i"
	  href="assets/images/img_photo.jpg"
	>
	  <img
		src="assets/images/img_photo.jpg"
		class="img-responsive"
		alt=""
	  />
	</a>
 </div>
```

參考文件：
https://fancyapps.com/fancybox/get-started/installation/
https://stackblitz.com/edit/angular-f6wtw1?file=src%2Fapp%2Fgallery%2Fgallery.component.ts