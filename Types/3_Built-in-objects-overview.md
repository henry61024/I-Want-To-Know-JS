## 物件型別 (Object Type) 概述

在 JavaScript 中除了 原始型別 以外的一切都是物件

### 內建物件 (Built-in Objects)

物件還有分成以下數種內建的子型別：

- String
- Number
- Boolean
- Object
- Function
- Array
- Date
- RegExp
- Error

其中`String`、`Number`、`Boolean` 有對應的原始型別

### 創建 內建物件 的方式

* **利用 `new` 來創建 內建物件**

  ```javascript
  var myStringObj = new String('hello world');
  console.log(myStringObj);				// String {"hello world"}

  var myNumberObj = new Number(123);
  console.log(myNumberObj);				// Number {123}

  var myBooleanObj = new Boolean(true);	// Boolean {true}
  console.log(myBooleanObj);

  var myObject = new Object();
  console.log(myObject);				// {}

  var myArray = new Array();
  console.log(myArray);					// []

  var myDate = new Date();
  console.log(myDate);					// Wed Dec 22 2017 21:47:10 GMT+0800
  ```

* **利用 字面值 ( Literals, 註1 ) 創建**

  * 適用於 `Object`、`Array`、`Function`、`RegExp` 。

  * Note：用 字面值方式 創建的物件與 `new` 出來的物件是一樣的。

    ```javascript
    var myLiteralObject = {};
    console.log(myLiteralObject);			// {}

    var myLiteralArray = [];
    console.log(myLiteralArray);			// []
    ```

### 辨別 物件型別

* **利用 `typeof()` 可以查看 變數/屬性/值 的內容為何種型別**

  承接上一個範例：

  ```javascript
  typeof myStringObj; 		// "object"
  typeof myNumberObj; 		// "object"
  typeof myBooleanObj; 		// "object"
  typeof myArray; 		// "object"
  ```

  我們可以看到，`String`，`Number`、`Boolean` 的型別都是物件，而非原始型別。

  但要怎麼知道物件屬於哪個子型別呢?

### 辨識 物件 子型別/建構子

*  **利用 `Object.prototype.toString.call()` 查看所屬的子型別：**

   會回傳 `[型別 子型別]`。 `call` 與 `prototype` 的詳細內容會在之後介紹。

   ```javascript
   Object.prototype.toString.call(myStringObj); 		// "[object String]"
   Object.prototype.toString.call(myNumberObj); 		// "[object Number]"
   Object.prototype.toString.call(myBooleanObj); 		// "[object Boolean]"
   Object.prototype.toString.call(myArray); 			// "[object Array]"
   ```

* **利用 `instanceOf` 可以查看某物件是不是屬於特定的子型別**

  會回傳 boolean，代表是不是屬於該子型別。

  ```javascript
  myStringObj instanceOf String; 		// "true"
  myStringObj instanceOf Number; 		// "false"

  myObject instanceOf Object			// "true"
  myLiteralObject instanceOf Object		// "true"

  myArray instanceOf Array			// "true"
  myLiteralArray instanceOf Array		// "true"
  ```

### 取出值

* **利用 `valueOf()` 可以取得物件的原始內容**

```javascript
myStringObj.valueOf();		// "hello world"
myNumberObj.valueOf();		// 123
myBooleanObj.valueOf();		// true
myArray.valueOf();		// []
```

### 與原始型別比較

- **物件允許寫入屬性，原始型別則無法**

  原始型別無法寫入屬性請參考上一篇。

  ```javascript
  'use strict'
  // test read property
  myStringObj.length;					// 11

  // test write property
  myStringObj.myProperty = 'testWrite';
  myStringObj.myProperty;				// "testWrite"
  ```
  ​



## 實作建議

### 盡量使用字面值來取代 `new`

對 `Object` 、`Array`、`Function`、`RegExp` 來說，用 字面值 與用 `new` 產生物件結果是一樣的。

對 `String`、`Number`、`Boolean` 來說，字面值宣告會產生原始型別，但因為 [必要時會自動強制轉型成物件型別的特性](https://ithelp.ithome.com.tw/articles/10192598)，所以在使用上與其物件型別並無不同。

也就是說，除了 `Date`、`Error` 這兩個型別以外必須使用 `new` 才能創建以外，其餘的內建物件在大部分的時候都是可以用 字面值 替代 `new` 的。

為了簡短、直觀、效能考量 ( 因為不需要 `new` )，請盡量使用字面值宣告物件。



## 小結

今天概述了 JavaScript 的內建物件 (Built-in Objects)，主要討論到的內容有：

* **創建 內建物件 的方式**
  * 利用 `new` 來創建 內建物件
  * 利用 字面值 (Literals) 創建
* **辨別 物件型別**
  * 利用 `typeOf`
* **辨識 物件 子型別/建構子**
  * `Object.prototype.toString.call()` 可以查看
  * `instanceOf` 會回傳 boolean，可以用來查看某物件是不是屬於特定的子型別
* **取出值**
  * 利用 `valueOf()` 可以取出物件的原始內容

由於我們知道，用 字面值 方式與用 `new` 產生出的物件，在使用上其實並無不同，因此在實作上為了簡短、直觀、效能考量，我們應該**盡量使用 字面值來取代 `new`** 方式以提高可讀性。



## 附註

### 1. 字面值 (Literals)

*以字面的方式來表示特定型別的固定值*

在 JavaScript 中，我們可以用字面值來表示以下幾種型別：

```javascript
'hello world';			// string literal
true;				// boolean literal
123;				// number literal
123.4;				// float number literal
null				// null literal
{};				// object literal
[];				// array literal
/ab+c/;				// regExp literal
```

如範例所示，我們可以用字面值來表示 `undefined` 以外的原始型別，而物件型別如 `Object`、`Array`、`RegExp` 也有其對應的字面值。



## 參考

[You Don't Know JS : Object](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md)

https://www.w3schools.com/js/js_arrays.asp

https://www.w3schools.com/js/js_object_definition.asp

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Grammar_and_types
