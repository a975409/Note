> 當瀏覽器加載頁面時，它會解析HTML並從中生成DOM property。 大多數形況下，HTML attribute會自動成為DOM property。
  
舉例來說，如果tag為`<div class = 'introduction'>`，則DOM property也會具有`div.class = 'introduction'`。  

#### attribute 與 property的映射不是一對一的，相異之處如下：
**Attributes**  
Attributes是我們寫在HTML的屬性，像是`<div type = 'text'>`。  
**Properties**  
Properties是存在在DOM objects中的屬性，像是`div.type`。

#### Attribute要符合相對應的Element class
> 當我們的element有符合標準的的attribute時，相對應的property就會生成。但是當element不符合標準時，相對應的property就不會生成。  

像是input元素有type這個attribute（符合標準），但body元素沒有type（不符合標準），所以不會生成相應的property。

參考連結：
https://teagan-hsu.coderbridge.io/2020/12/28/javascript-dom-attribute-property/