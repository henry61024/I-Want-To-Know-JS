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

string 算是 JavaScript 中，比較 "普通" 的型別，相較於其他的型別來說，他並沒有太多出人意料的行為。

String 物件是由 index 跟 字元 組成的

## number



## boolean

boolean總共只會有兩種值：`true` & `false`，雖然看似簡單，但使用它的精隨可以讓你的程式優雅許多。

### 宣告

boolean 的 literal 宣告方式非常簡單，你只要把 `true` 或者 `false` 指派給一個變數就好。

// literal ???

```
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
!!Infinity;			// true
!!{};				// true
!![];				// true
```

### 實作建議

熟稔 `truthy` & `falsy` 的技巧可以讓你的程式更簡潔明瞭。

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

???





## undefined

*代表沒有值，是 JavaScript 的預設值*

### 宣告 ???

undefined 的宣告方式很簡單，直接將 undefined 指派給變數，或者乾脆不要給值

```javascript
var nothing = undefined;
console.log(nothing);			// undefined

var nothingInside;
console.log(nothingInside);		// undefined
```

### 注意事項

並不是 JSON-Safe，因為為 JavaScript 獨有

### 實作建議

* 單純宣告時不需要特別把 undefined 指派給變數，除非是要覆蓋
* 當預期看到值的時候，卻得到 undefined ，就代表該 變數/屬性 根本沒有被操作過，很有可能是漏了操作，又或者是你操作到其他值了。這個概念可以作為 debug 的依據



## null

*在JavaScript中，NULL代表"無"，是人為賦予的值*

### 字面值 (Literal)



```
var nothing = null;
```





### 注意事項



 [JSON-Safe]()，因為大部分程式通用

```javascript
typeOf null 			// "object"
```



### 實作建議

* 避免使用 `typeOf null`

  如前一篇所提，`typeOf null === "object"`，如果你不清楚這個特性，或者將其他程式的 null 使用方式帶入 JavaScript 的話，就很可能出錯。如果你很清楚這個特性的話，那你更不應該用了，畢竟你應該不會想將自己的程式建立在一個讓人容易誤會的 JavaScript 古老 Bug 上吧?



## 小結

字面值用附註的方式?



## 參考資料

保哥 JavaScript

飛肯設計: 進階 JS 班

JavaScript : The good parts

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Grammar_and_types