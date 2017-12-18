## 型別 (Types)

在 JavaScript 中，總共有六種型別

- string
- number
- boolean
- null
- undefined
- object

而這六種型別又可以分成以下兩大類別

- 原始型別
- 物件型別





## 原始型別 (Primitive Type) 概述

原始型別為單一值，是 JavaScript 中最基本的單位

- string
- number
- boolean
- null
- undefined

每個 primitive 詳細的內容在下一篇介紹

### 特性

- **原始型別是單一值，沒有屬性**

  如開頭所說，原始型別為單一值，因此不會有屬性，我們可以用 `console.dir()` 來確定這點。

  ```javascript
  console.dir('hello world');		// "hello world"
  ```


- **原始型別是不可變異的 ( immutable )**

  簡單來講，你在 JavaScript 中能改變變數/屬性 中儲存的值，但是你無法改變值本身

  `var a = 1` 中，你可以改變變數 `a` 的內容，但你無法改變 `1` 的值

- **讀取原始型別的屬性時，JavaScript 會將之強制轉型為 對應的物件型別** ( 除了 `null` & `undefined` )

  為了方便性，JavaScript 用強制轉型來允許原始型別操作其物件型別的屬性

  上面已經用範例驗證原始型別沒有屬性這點，接著對原始型別讀取屬性確認它是否能夠取得其對應物件型別的屬性，最後測試對原始型別寫入屬性是否可以成功。

  - **對 原始型別 讀取屬性時，會強制轉型為 對應的物件型別** ：

    這裡試試原始型別 `string` 會不會強制轉型為 `String `物件型別來取得內建屬性 `.length` 。結果如我們所想，就算原始型別本身沒有屬性，我們還是能夠使用對應的物件型別的屬性。

    ```javascript
    'hello world'.length;		// 11
    ```

  - **對 原始型別 寫入屬性 會出現錯誤：**

    最後嘗試對原始型別進行寫入。我們可以看到，在非 `strict-mode` 模式下看似允許對原始型別寫入屬性，但實際上他的值並無法被寫入。而在 `strict-mode` 模式下則是直接禁止對原始型別寫入屬性的。

    這部分還是回到 '原始型別為單一值，因此不會有屬性' 的概念，你不能對一個單一值加上額外的屬性，而 JavaScript 也沒有提供這個額外的功能。

    ```javascript
    'hello world'.length = 1;
    'hello world'.length		// 11

    'use strict';
    'hello world'.length = 1;	// Uncaught TypeError
    ```


### 辨別原始型別

用 `typeof` 可以查看 變數/屬性/值 的內容為何種型別 (除了 `null` 以外 )

```javascript
var myString = 'hello world';
typeof myString; 			// "string"
typeof 'hello world'; 		// "string"

var myNumber = 123;
typeof myNumber; 			// "number"
typeof 123; 				// "number"

var myBoolean = true;
typeof myBoolean; 			// "boolean"
typeof true; 				// "boolean"

var myUndefined = true;
typeof myUndefined; 		// "undefined"
typeof undefined; 			// "undefined"

var myNull = true;
typeof myNull; 				// "object"
typeof null; 				// "object"
```

其中，`typeof null === "object" ` 是一個早期 JavaScript 設計時候的 Bug，實際上 `null` 就應該是 `null` 。



## 小結

JavaScript 中分為六個型別與兩大類別，這篇主要介紹 原始型別 (Primitive Type) 的一些特性，分別為：

* 原始型別是單一值，沒有屬性
* 原始型別是不可變異的 ( immutable )
* 讀取原始型別的屬性時，JavaScript 會將之強制轉型為 對應的物件型別

另外，也學到能使用 `typeof` 來查看除了 `null` 以外的原始物件型別

下次將介紹各個原始型別以及在使用時應該注意的事項
