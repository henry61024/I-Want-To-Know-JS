# 原始型別個別介紹

無論是哪個語言，掌握型別的特性都是非常重要的基本功。這點在 JavaScript 中尤其重要，當不了解型別的特性時，總是會在意想不到的地方被 JavaScript 的地雷炸個粉身碎骨，然而當你聊若指掌時，你便能寫出更為優雅的程式。

// Quote 優良部分 http://shop.oreilly.com/product/9780596517748.do

讓我們複習一下 JavaScript 原始型別有哪些：

- string
- number
- boolean
- null
- undefined


今天我們就要來深入了解每個原始型別的特性。



## string

### 字面值

利用 `''` 或者 `""` 將字串包起來，就會是 string 的字面值

```javascript
var myString = 'hello world';
```

### 特性

* **String 的索引就是物件的屬性**

  String 物件是由 索引 跟 字元 組成的，舉例來說：

  ```javascript
  var myStringObj = String('hello world');
  ```

  有沒有覺得跟 Object 的字面值很像呢? 沒錯，String 索引就是物件的屬性，因此我們可以把他想成：

  ```javascript
  {
      0: 'h',
      1: 'e',
      2: 'l',
      3: 'l',
      4: 'o',
      5: ' ',
      6: 'w',
      7: 'o',
      8: 'r',
      9: 'l',
      10: 'd'
  }
  ```

  因此我們便能夠利用屬性讀取 string 的每個字元：

  ```javascript
  myString[0] = 'h';
  ```

  接著再複習一次原始型別提過的事情：讀取原始型別屬性時會觸發強制轉型

  ```javascript
  var myString = 'hello world';
  console.log(myString[0]);				// "h"
  console.log(myString[5]);				// " "
  console.log(myString[11]);				// "d"
  ```

  也就是不論是 string 原始型別，或是 string 物件，我們都可以利用索引拿到各個字元。

### 內建方法

* String 有非常豐富的內建方法可以讓我們很方便的對可以對字串做 插入、替代、尋找 等動作
  * 參考：https://www.w3schools.com/jsref/jsref_obj_string.asp



## number

### 字面值

number 的字面值有很多種，包括

* 正值
* 負值
* 浮點數
* 八進位
* 十進位
* 十六進位
* 科學記號
* Infinity
* NaN (Not an Number)

```javascript
var myNumber = 100,
	myNegativeNumber = -100,
	myfloatNumber = 0.3,
	myOctal = 012,						// 10
	myHex = 0xFF,						// 255
	myScientificNotation = 513e-5,		// 513e-5
	myInfinity = Infinity,
	myNaN = NaN;
```

### 強制轉型

* **Number()，+()**

  * 強制轉型為 **十進位數字** 的方法
  * 若無法正確轉化成字串的話，就會顯示 NaN

  ```javascript
  Number('0100');			// 100
  Number('0100.1');		// 100.1
  Number('100abc');		// NaN
  Number(undefined);		// NaN
  +'0100';				// 100
  +'0100.1';				// 100.1
  +'100abc';				// NaN
  +undefined;				// NaN
  ```

* **parseInt ()**

  * 強制轉型為 整數
  * 第二個參數可以選擇要以何種進位轉換為十進位
  * 無法 parse 科學記號
  * 遇到非數字時會只把前面的數字 parse 出來

  ```javascript
  parseInt('0100');				// 100
  parseInt('0100', 8);			// 64
  parseInt('0100.1');				// 100
  parseInt('100abc');				// 100
  parseInt(undefined);			// NaN
  ```

*  **parseFloat()**

  * 強制轉型為 十進位浮點數
  * 可以 parse 科學記號
  * 遇到非數字時會只把前面的數字 parse 出來

  ```javascript
  parseFloat('0100');				// 100
  parseFloat('0100.1');			// 100.1
  parseFloat('100abc');			// 100
  parseFloat(undefined);			// NaN
  ```

### 注意事項

* JavaScript 浮點數運會有誤差值

  因為是依據 [IEEE 754 的雙精度 (64位元)](https://zh.wikipedia.org/wiki/IEEE_754) 去做轉換，因此在做浮點數運算的時候最好搭配 `parseFloat()` 與  `toFixed()` 來確保正確性。

  ```javascript
  0.1 + 0.2 = 0.30000000000000004;
  0.1 + 0.2 === 0.3;									// false
  parseFloat((0.1 + 0.2).toFixed(10)) === 0.3;		// true
  ```

* NaN

  * NaN (Not a Number) 卻屬於 Number

    ```javascript
    typeof NaN;			// number
    ```

  * NaN 不等於 NaN，不論是用 `===` 或 `==`

    ```javascript
    NaN === NaN			// false
    NaN == NaN			// false
    ```



## boolean

boolean總共只會有兩種值：`true` & `false`，雖然看似簡單，但使用它的精隨可以讓你的程式優雅許多。

### 字面值

boolean 的 字面值就是 `true` 或者 `false` 。

// literal ???

```javascript
var myBoolean = true;
var myBoolean = false;
```

除此之外，我們也能夠把其他型別的變數強制轉型為 boolean，如果其他型別，則我們會

### 強制轉型

在 JavaScript 中，有非常多的方法可以將值強制轉型為 boolean，以下將列舉幾種常用的

* **Boolean(*value*)：**

  強制轉型為 boolean

  ```javascript
  Boolean('hello world');		// true
  ```

* **!!*value*：**

  Boolean(*value*) 的語法糖衣

  ```javascript
  !!'hello world';			// true
  ```

* **!*value*：**

  反向強制轉型為 boolean

  ```javascript
  !'hello world';			// false
  ```



除此之外，也會有很多的邏輯判斷會觸發強制轉型，其中最常見就是

* **if (*value*)：**

  大家都知道 if () 是用 `true`、`false` 來判斷是否要執行內部的邏輯，在 JavaScript 中，如果我們把非 boolean 值放入 if () 裡面就會觸發強制轉型

  ```javascript
  var hello = 'hello world';
  if (hello) {
  	console.log(hello);		// "hello world"
  }
  ```

### falsy

如果一個值被強制轉型為 boolean 的結果為 `false` 的話，則我們就稱此值為 `falsy`。

在 JavaScript 中，只有以下五種值為 `falsy`：

```javascript
!!false				// false
!!0					// false
!!''				// false
!!null				// false
!!undefined			// false
!!NaN				// false
```

### truthy

如果一個值被強制轉型為 boolean 的結果為 `true` 的話，則我們就稱此值為 `truthy`。

除了 `falsy` 的五種值以外，都是 `truthy`。

```javascript
!!' ';				// true
!!1;				// true
!!-Infinity;		// true
!!{};				// true
!![];				// true
```

### 實作建議

* **善用 `truthy` & `falsy` 技巧可以讓你的程式更簡潔明瞭**

  * **範例 1：**

    舉個例子來說，假如今天你傳入了一個參數，而你確定參數只可能會是 `string`或 `undefined` 的話，那如果要做以下的判斷：

    ```javascript
    if ((value === undefined) && (value === '')) {
    	// do something
    }
    return Boolean(value1) && Boolean(value2)
    ```

    那不如就簡化成：

    ```javascript
    if (!value) {
    	// do something
    }
    ```

    更為優雅

  * **範例 2：**

    或說你要回傳一個 boolean 值

    ```javascript
    function returnBoolean() {
      	// ..
    	return !Boolean(value1) && !Boolean(value2);
    }
    ```

    相當於：

    ```javascript
    function returnBoolean() {
      	// ..
    	return !value1 && !value2;
    }
    ```

    利用語法糖衣可以有效提高易讀性。



## undefined

*代表沒有值，是 JavaScript 的預設值*

### 指派

undefined 的指派方式很簡單，直接將 undefined 指派給變數，或者乾脆不要給值

```javascript
var nothing = undefined;
console.log(nothing);			// undefined

var nothingInside;
console.log(nothingInside);		// undefined
```

有沒有發現這邊並沒有說 `var nothing = undefined` 是一個字面值呢? 其實程式中直接打出的 `undefined` 是一個全域變數，他內部的值就是 `undefined`，而他們的型別也是 `undefined`。因此若你直接指派 undefined 給一個變數，這並不會是 字面值，而會是將 `undefined` 全域變數指派給另一個變數的動作。可以參考：

https://msdn.microsoft.com/zh-tw/library/dae3sbk5

### 注意事項

* 並不是 JSON-Safe，因為 undefined 是 JavaScript 獨有的值

### 實作建議

* 單純宣告時不需要特別把 undefined 指派給變數，除非是要覆蓋
* 當預期看到值的時候，卻得到 undefined ，很可能代表該 變數/屬性 根本沒有被操作過，有機會是漏了操作，也或者是你操作到其他值了。這個概念可以作為一個 debug 的依據。





## null

*在 JavaScript 中，NULL代表"無"*

### 字面值 (Literal)

null 的字面值就是 null。

```
var nothing = null;
```

### 注意事項

* **`typeof null` 是 `object`**

  ```
  typeOf null 			// "object"
  ```

  算是 JavaScript 的 Bug，容易讓人誤會也不合邏輯，因此務必不要拿 null 來做 `typof` 的判斷。

* **null 是人為賦予的值，而非 JavaScript 賦予的預設值**

  當你看到一個 變數/屬性 為 null 時，就代表他一定有個地方被指派為 null 過，因為不像是 undefined 代表 JavaScript 的預設值。如果在非預期的狀況下看到 null 的話，這個概念可以作為 debug 的依據。

* **因為大部分程式都有 null，因此 null 是 [JSON-Safe]()**

### 實作建議

* 避免使用 `typeOf null`

  如前一篇所提，`typeOf null === "object"`，如果你不清楚這個特性，或者將其他程式的 null 使用方式帶入 JavaScript 的話，就很可能出錯。如果你很清楚這個特性的話，那你更不應該用了，畢竟你應該不會想將自己的程式建立在一個讓人容易誤會的 JavaScript 古老 Bug 上吧?



## 小結

此篇介紹了 JavaScript 中，每個原始型別的特性與注意事項



## 參考資料

保哥 JavaScript

飛肯設計: 進階 JS 班

JavaScript : The good parts

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Grammar_and_types

https://www.fooish.com/javascript/number/

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Obsolete_Pages/Obsolete_Pages/Obsolete_Pages/%E9%A0%90%E5%85%88%E5%AE%9A%E7%BE%A9%E7%9A%84%E5%87%BD%E6%95%B8/parseInt_%E5%92%8C_parseFloat_%E5%87%BD%E6%95%B8