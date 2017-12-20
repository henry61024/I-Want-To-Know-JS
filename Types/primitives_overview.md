## 型別 (Types)

在 JavaScript 中，總共有六種型別：

- string
- number
- boolean
- null
- undefined
- object

而這六種型別又可以分成以下兩大類別：

- 原始型別
- 物件型別





## 原始型別 (Primitive Type) 概述

*原始型別是 JavaScript 中最基本的單位，它是一個值，因為並不是物件，所以沒有任何屬性*

- string
- number
- boolean
- null
- undefined

### 特性

- **原始型別是一個值，沒有屬性**

- **原始型別是不可變異的 ( immutable )**

  在 JavaScript 中，你能改變變數/屬性 中儲存的值，但是你無法改變他記憶體位置中的內容。

  舉例來說，`var a = 1` 中，你可以改變變數 `a` 的內容，但你沒有任何手段可以把儲存 `1` 這個值的記憶體位置寫入其他內容。更多詳細的內容將在之後介紹 JavaScript傳值還是傳址時討論。

- **必要時，JavaScript 引擎會將原始型別強制轉型為 對應的物件型別** ( 除了 `null` & `undefined` )

  為了方便性，JavaScript 用強制轉型來允許原始型別操作其物件型別的屬性。請注意，因為`null` & `undefined` 沒有對應的物件型別，所以自然也不會被強制轉型。

  接下來我們會對原始型別讀取屬性確認它是否能夠取得其對應物件型別的屬性，最後測試對原始型別寫入屬性是否可以成功。

  - **對 原始型別 讀取屬性時，會強制轉型為 對應的物件型別** ：

    這裡試試原始型別 `string` 會不會強制轉型為 `String `物件型別來取得內建屬性 `.length` 。結果如我們所想，就算原始型別本身沒有屬性，我們還是能夠使用對應的物件型別的屬性。

    ```javascript
    'hello world'.length;		// 11
    ```

  - **對 原始型別 寫入屬性，會強制轉型為對應的物件型別，但會是無效/錯誤的：**

    最後嘗試對原始型別進行寫入。我們可以看到，在非 `strict-mode` 模式下看似允許對原始型別寫入屬性，但並沒有成功寫入。

    ```javascript
    'hello world'.length = 1;
    'hello world'.length		// 11
    ```

    到這邊為止，我們無法確定為何會寫入失敗。接著試試在 `strict-mode` 下執行。

    ```javascript
    'use strict';
    'hello world'.length = 1;	// Uncaught TypeError: Cannot assign to read only property 'length' of string 'hello world'
    ```

    他跳出了 `Cannot assign to read only property 'length' of string` 的錯誤訊息，也就代表，即便在寫入屬性時，原始型別也會被強制轉型為 對應的物件型別，會無法寫入只是因為 `length` 有 `read-only` 的屬性。

    那假如我們試著寫進一個新的屬性呢?

    ```javascript
    'use strict';
    'hello world'.myProperty = 1;	// Uncaught TypeError: Cannot create property 'myProperty' on string 'hello world'
    ```

    這次的錯誤訊息是 `Cannot create property 'myProperty' on string 'hello world'`，因此得知，當我們對原始型別 寫入屬性，JavaScript 引擎也會將它強制轉型為對應的物件型別，但這操作會是無效/錯誤的。




### 辨別原始型別

用 `typeof` 可以查看 變數/屬性/值 的內容為何種型別 (除了 `null` 以外 )

```javascript
var myString = 'hello world';
typeof myString;			// "string"
typeof 'hello world';		        // "string"

var myNumber = 123;
typeof myNumber; 			// "number"
typeof 123; 				// "number"

var myBoolean = true;
typeof myBoolean; 			// "boolean"
typeof true; 				// "boolean"

var myUndefined = true;
typeof myUndefined;			// "undefined"
typeof undefined; 			// "undefined"

var myNull = true;
typeof myNull; 				// "object"
typeof null; 				// "object"
```

其中，`typeof null === "object" ` 是一個早期 JavaScript 設計時候的 Bug，實際上 `null` 就應該是 `null` 。



## 小結

JavaScript 中分為六個型別與兩大類別，這篇主要介紹 原始型別 (Primitive Type) 的一些特性，分別為：

* 原始型別是一個值，沒有屬性
* 原始型別是不可變異的 ( immutable )
* 必要時，JavaScript 引擎會將原始型別強制轉型為 對應的物件型別 ( 除了 `null` & `undefined` )

另外，也學到能使用 `typeof` 來查看除了 `null` 以外的原始物件型別。

下次將介紹各個原始型別以及在使用時應該注意的事項。



## 參考

[You Don't Know JS : Object](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md)

https://www.w3schools.com/js/js_object_definition.asp

https://developer.mozilla.org/en-US/docs/Glossary/Primitive

https://javascriptweblog.wordpress.com/2010/09/27/the-secret-life-of-javascript-primitives/
