以下`<script></script>`標籤，只能用在`src`資料夾內的`index.html`：
```html
<script src="assets/js/snap.svg-min.js"></script>
<script src="assets/js/modernizr.custom.js"></script>
<!--或在script寫JS語法 -->
    <script>
      var useragent = navigator.userAgent;
      useragent = useragent.toLowerCase();
      if (
        useragent.indexOf("iphone") != -1 ||
        useragent.indexOf("ipad") != -1 ||
        useragent.indexOf("ipod") != -1
      ) {
        document.addEventListener("touchstart", function () {}, false);
      }
    </script>
```

其餘`component.html`是無法新增`<script></script>`標籤，就算新增了也毫無作用
