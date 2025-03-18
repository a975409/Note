
> 可讓index.html或angular內的像是component或service，皆可呼叫使用

1. 在service建立`function`：
```typescript
(window as any).angularLogout = () => {
      this.logout();
    };
```

可在`index.html`呼叫：
```html
<script>
if (window.angularLogout) {
          window.angularLogout();
        }
</script>
```

2. 在index.html建立`function`：
```javascript
window.updateAuthLinks = function (isLoggedIn) {};
```

可在`service`呼叫：
```typescript
if (typeof (window as any).updateAuthLinks === 'function') {
      (window as any).updateAuthLinks(this.isLogined());
    }
```
