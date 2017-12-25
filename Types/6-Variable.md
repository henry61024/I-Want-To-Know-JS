

## 變數

*JavaScript 的變數是一個容器，內部儲存記憶體位置的指向*

###使用

JavaScript 利用 `var` 配上名稱來宣告變數

```javascript
var a = 'hello world';
```

請注意變數名稱不能包含符號，也不能是[保留字](https://msdn.microsoft.com/zh-tw/library/0779sbks(v=vs.94).aspx)，否則會出現 `SyntaxError`

```javascript
var void = 'hello world';			// Uncaught SyntaxError: Unexpected token void
var "a-1" = 1;						// Uncaught SyntaxError: Unexpected string
```

### 變數是弱型別的

在如 `C++`、`Java` 等強行別語言中，每個型別都有對應的宣告語法，而宣告出來的變數也無法再被指派為其他型別的值，相較之下

 **JavaScript 中的變數：**

* 並沒有綁定任何的型態
* 所有的變數都是經由 `var` 宣告
* 宣告後的變數也可以被自由指派為其他型別的值

舉例來說

```javascript
var a = 'hello world';
console.log(a);						// "hello world"
a = 123;
console.log(a);						// 123
```

### 變數無法被刪除

在 JavaScript 中，變數是無法被刪除的：

```javascript
var a = 'hello world';
console.log(a);						// "hello world"
delete a;							// false
console.log(a);						// "hello world"
```

通常看到這種不能通過的操作，就會想到 `'use strict'` 模式很可能擋下，因此同樣有很實驗精神的測試看看加上 `'use strict'` 能不能通過：

```javascript
'use strict'
var a = 'hello world';
console.log(a);						// "hello world"
delete a;							// Uncaught SyntaxError: Delete of an unqualified identifier in strict mode.
console.log(a);						// "hello world"
```

結果是不能過， `'use strict'` 模式不允許你刪除變數。

### 變數 與 根物件

- **在全域範疇 ( Global Scope ) 中宣告的 變數 同時會是 根物件 的屬性**

  ```javascript
  var a = 'hello world';
  console.log(window.a);					// "hello world"
  ```

- **非 `strict mode` 下，不用 `var` 宣告就使用未曾出現的變數名稱，則該變數名稱會變成根物件的屬性**

  ```javascript
  function innerTest() {
  	innerVar = 'Im innerVar';
  }
  innerTest();
  console.log(innerVar);					// "Im innerVar"
  console.log(window.innerVar);			// "Im innerVar"
  ```

  在 `'use strict'` 下則不允許使用未宣告的變數。

  讓我們看看同樣的程式碼加上 `'use strict'` 會發生什麼事。

  ```javascript
  'use strict'
  function innerTest() {
  	innerVar = 'Im innerVar'; 		//  Uncaught ReferenceError: innerObj is not defined
  }
  innerTest();
  console.log(innerVar);
  console.log(window.innerVar);
  ```

  我們能看到在 `innerObj` 一出現就產生了 `Uncaught ReferenceError` 的錯誤訊息，這是因為 `'use strict'` 下的 JavaScript 試圖往上尋找 `innerObj` 這個未宣告的變數，而找不到的結果就是回傳 `ReferenceError`。

- **在全域範疇 ( Global Scope ) 中宣告變數**  vs. **不用 `var` 宣告就使用未曾出現的變數名稱**

  這兩點達成的效果看起來很類似，我們都可以在任何地方取得他的值，然而還是有一點不同：

  **不同點：**不用 `var` 宣告就使用未曾出現的變數名稱，則他只會是根物件的屬性，但不會是一個屬性

  我們利用 **變數無法被刪除 **這個規則就可以很容易的驗證這點：

  ```javascript
  var a = 'hello world';
  delete a;								// false
  console.log(window.a);					// "hello world"

  function innerTest() {
  	innerVar = 'Im innerVar';
  }
  innerTest();
  delete innerTest();						// true
  console.log(window.innerVar);			// Uncaught ReferenceError: innerVar is not defined at <anonymous>:1:1
  ```

  我們可以看到，使用 `var` 宣告的 `a` 無法被刪除，而並未使用 `var` 宣告的 `innerVar` 則成功被刪除，由此可得知 `innerVar` 並不真的是一個變數，而是根物件 `window` 的屬性而已。

### 變數與屬性

變數與屬性很相似，他們能夠裝載任何值，

* **相同處**
  * 都是儲存記憶體位置的容器


* **不同處**

  * 變數無法被刪除

    ```javascript
    var myVar = 'Variable';
    myProp = 'Property'
    console.log(window.myVar);			// "Variable"
    console.log(window.myProp);			// "Property"

    delete myVar;
    delete myProp;
    console.log(window.myVar);			// "Variable"
    console.log(window.myProp);			// undefined
    ```


### 實作建議

* **JS 一個變數盡量保持使用同一種型別** (除了能容許預設值的 `undefined` 外)
  * 否則在程式碼越來越常的狀況下，很容易會造成混亂，且此變數代表的意義也會變得模糊不清。


* **避免汙染全域變數**

  在全域變數新增 變數/屬性 看似很方便，但會讓你之後命名的限制越來越多，甚至是很容易不小心就誤用了其他意義的變數，換句話說，就是會讓你的程式彈性、維護性大幅降低。

  為了確保不汙染全域變數，利用注意事項中學到的知識，我們至少要做到以下兩點。

  * 無論在任何地方**宣告變數時，務必使用 `var`** ，否則會變成全域變數的屬性
  * **盡量不要在全域範疇 ( Global Scope ) 中宣告變數**，否則會變成全域變數的屬性




## 參考

[保哥 JavaScript 開發實戰：核心概念篇](https://www.accupass.com/event/1710170207181263097416)

[JavaScript : The good parts](http://shop.oreilly.com/product/9780596517748.do)

http://www.codedata.com.tw/javascript/essential-javascript-variable/