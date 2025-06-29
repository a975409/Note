參考文件：
[在浏览器中使用JavaScript module(模块) – WEB骇客 (webhek.com)](https://www.webhek.com/post/ecmascript-modules-in-browsers/)

引用模組範例如下：
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Scrollable Table with Sticky Header</title>
  </head>
  <body>
    <script type="module">
      import Base64 from "./Base64Extensions.js";

      let id = "1";
      console.log(Base64.encodeUrlBase64(id));
    </script>
  </body>
</html>
```

※ Base64Extensions.js 與 Base64.js 是用來做Base64編碼用的

Base64Extensions.js
```jsx
import Base64 from "./Base64.js";

//網址使用base64的方案  By Leeson_Hsu
export default class Base64Url {
  constructor() {}
  /**
   * 傳入並轉為BASE64字串
   * @param {*} sourceString 來源字串
   */
  static encodeUrlBase64(sourceString) {
    //使用暫時的變數來取代代入值，避免之後被覆寫
    var base64String = Base64.encode(sourceString);
    console.log("replace!!");
    //base64在URL會有下列三種符號會有問題

    base64String = base64String.split("=")[0];
    var replacements = {
      "+": "-",
      "/": "_",
    };
    //必須使用正則覆蓋，多重replace會有異常沒覆蓋的問題
    var finalString = Base64Url.replace(base64String, replacements);
    return finalString;
  }

  /**
   * 傳入一個BASE64字串，並將其解開
   * @param {*} base64String
   */
  static decodeUrlBase64(base64String) {
    //使用暫時的變數來取代代入值，避免之後被覆寫
    var tmpString = "" + decodeURIComponent(base64String);
    //base64在URL會有下列三種符號會有問題
    var replacements = {
      "-": "+",
      _: "/",
    };
    //必須使用正則覆蓋，多重replace會有異常沒覆蓋的問題
    var finalBase64 = Base64Url.replace(tmpString, replacements);

    switch (finalBase64.length % 4) {
      case 0:
        break; // No pad chars in this case
      case 2:
        finalBase64 += "==";
        break; // Two pad chars
      case 3:
        finalBase64 += "=";
        break; // One pad char
    }

    return Base64.decode(finalBase64);
  }

  /**
    * 使用正則覆蓋，否則原始多重replace會造成解析過慢來不及覆蓋的問題
    * @param {*} SourceString 
    * @param {*} replacements 
    * 
    *  var replacements = {
           "a": "d"
           , "b": "e"
           , "c": "f"
       };
    */
  static replace(SourceString, replacements) {
    Array.prototype.each = function (trans) {
      for (var i = 0; i < this.length; i++) this[i] = trans(this[i], i, this);
      return this;
    };
    Array.prototype.map = function (trans) {
      return [].concat(this).each(trans);
    };
    RegExp.escape = function (str) {
      return new String(str).replace(/([.*+?^=!:${}()|[\\]\\/\\\\])/g, "\\\\$1");
    };
    function properties(obj) {
      var props = [];
      for (var p in obj) props.push(p);
      return props;
    }
    var regex = new RegExp(
      properties(replacements).map(RegExp.escape).join("|"),
      "g"
    );
    SourceString = SourceString.replace(regex, function ($0) {
      return replacements[$0];
    });
    return SourceString;
  }
}
```

Base64.js
```jsx
/**
 *
 *  Base64 encode / decode
 *  <http://www.webtoolkit.info>
 *
 **/

export default class Base64 {
  constructor() {}
  // public method for encoding
  static encode(input) {
    var output = "";
    var chr1, chr2, chr3, enc1, enc2, enc3, enc4;
    var i = 0;

    input = Base64._utf8_encode(input);

    while (i < input.length) {
      chr1 = input.charCodeAt(i++);
      chr2 = input.charCodeAt(i++);
      chr3 = input.charCodeAt(i++);

      enc1 = chr1 >> 2;
      enc2 = ((chr1 & 3) << 4) | (chr2 >> 4);
      enc3 = ((chr2 & 15) << 2) | (chr3 >> 6);
      enc4 = chr3 & 63;

      if (isNaN(chr2)) {
        enc3 = enc4 = 64;
        //console.log("enc3 = enc4 = 64");
      } else if (isNaN(chr3)) {
        enc4 = 64;
        //console.log("enc4 = 64");
      }

      output =
        output +
        Base64._keyStr.charAt(enc1) +
        Base64._keyStr.charAt(enc2) +
        Base64._keyStr.charAt(enc3) +
        Base64._keyStr.charAt(enc4);

      //console.log("output:"+output);
    } // Whend

    return output;
  } // End Function encode

  static encodeBase64Json(input) {
    //Base64解碼
    var jsonString = JSON.stringify(input);
    var base64Json = Base64.encode(window.encodeURIComponent(jsonString));
    console.log("[encodeBase64Json]=>" + base64Json);
    return jsonObject;
  }

  // public method for decoding
  static decode(input) {
    var output = "";
    var chr1, chr2, chr3;
    var enc1, enc2, enc3, enc4;
    var i = 0;
    input = input.replace(/[^A-Za-z0-9\\+\\/\\=]/g, "");
    while (i < input.length) {
      enc1 = Base64._keyStr.indexOf(input.charAt(i++));
      enc2 = Base64._keyStr.indexOf(input.charAt(i++));
      enc3 = Base64._keyStr.indexOf(input.charAt(i++));
      enc4 = Base64._keyStr.indexOf(input.charAt(i++));

      chr1 = (enc1 << 2) | (enc2 >> 4);
      chr2 = ((enc2 & 15) << 4) | (enc3 >> 2);
      chr3 = ((enc3 & 3) << 6) | enc4;

      output = output + String.fromCharCode(chr1);

      if (enc3 != 64) {
        output = output + String.fromCharCode(chr2);
      }

      if (enc4 != 64) {
        output = output + String.fromCharCode(chr3);
      }
    } // Whend
    output = Base64._utf8_decode(output);
    return output;
  } // End Function decode

  // private method for UTF-8 encoding
  static _utf8_encode(string) {
    var utftext = "";
    string = string.replace(/\\r\\n/g, "\\n");

    for (var n = 0; n < string.length; n++) {
      var c = string.charCodeAt(n);

      if (c < 128) {
        utftext += String.fromCharCode(c);
      } else if (c > 127 && c < 2048) {
        utftext += String.fromCharCode((c >> 6) | 192);
        utftext += String.fromCharCode((c & 63) | 128);
      } else {
        utftext += String.fromCharCode((c >> 12) | 224);
        utftext += String.fromCharCode(((c >> 6) & 63) | 128);
        utftext += String.fromCharCode((c & 63) | 128);
      }
    } // Next n

    return utftext;
  } // End Function _utf8_encode

  // private method for UTF-8 decoding
  static _utf8_decode(utftext) {
    var string = "";
    var i = 0;
    var c, c1, c2, c3;
    c = c1 = c2 = 0;

    while (i < utftext.length) {
      c = utftext.charCodeAt(i);

      if (c < 128) {
        string += String.fromCharCode(c);
        i++;
      } else if (c > 191 && c < 224) {
        c2 = utftext.charCodeAt(i + 1);
        string += String.fromCharCode(((c & 31) << 6) | (c2 & 63));
        i += 2;
      } else {
        c2 = utftext.charCodeAt(i + 1);
        c3 = utftext.charCodeAt(i + 2);
        string += String.fromCharCode(
          ((c & 15) << 12) | ((c2 & 63) << 6) | (c3 & 63)
        );
        i += 3;
      }
    } // Whend
    return string;
  } // End Function _utf8_decode
}
Base64._keyStr =
  "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
```