> 無須在HTML引用CSS，只要在 angular.json 引用即可

假設CSS存放在`src\assets\css`資料夾下，則`angular.json`設定如下：
```JSON
"build": {
          "builder": "@angular-devkit/build-angular:application",
          "options": {
            "outputPath": "dist/system-wanhai-frontend",
            "index": "src/index.html",
            "browser": "src/main.ts",
            "polyfills": ["zone.js"],
            "tsConfig": "tsconfig.app.json",
            "assets": ["src/assets"],//需設定為"src/assets"
            "styles": [//需引用的CSS路徑
              "src/assets/css/animate.css",
              "src/assets/css/bootstrap.css",
              "src/assets/css/bootstrap.min.css",
              "src/assets/css/custom.css",
              "src/assets/css/style.css"
            ],
            "scripts": []
          },
          "configurations": {
            "production": {
              "budgets": [
                {
                  "type": "initial",
                  "maximumWarning": "500kB",
                  "maximumError": "1MB"
                },
                {
                  "type": "anyComponentStyle",
                  "maximumWarning": "4kB",
                  "maximumError": "8kB"
                }
              ],
              "outputHashing": "all"
            },
            "development": {
              "optimization": false,
              "extractLicenses": false,
              "sourceMap": true,
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.development.ts"
                }
              ]
            }
          },
          "defaultConfiguration": "production"
        },

"test": {
          "builder": "@angular-devkit/build-angular:karma",
          "options": {
            "polyfills": ["zone.js", "zone.js/testing"],
            "tsConfig": "tsconfig.spec.json",
            "assets": ["src/assets"],//需設定為"src/assets"
            "styles": [//需引用的CSS路徑
              "src/assets/css/animate.css",
              "src/assets/css/bootstrap.css",
              "src/assets/css/bootstrap.min.css",
              "src/assets/css/custom.css",
              "src/assets/css/style.css"
            ],
            "scripts": []
          }
        }
```