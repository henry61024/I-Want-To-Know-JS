# JavaScript Coding Style

不論在各種程式語言中，保持良好的 Coding Style 都是很重要的。

良好的 Coding Style 可以使程式邏輯更易於瞭解，不但增加專案的易讀性以及可維護性，也能間接加快開發速度。

本篇中以下會列出幾點個人遵守的一些 Coding Style，其中可能會參雜一些小技巧，並會切割成幾個部分來說明。

請注意，有些 Coding Style 是必須遵守的；有些 Coding Style 則是會根據個人/團隊的使用習慣、見解 而有所改變，這部分的 Coding Style 只是提出一些建議與經驗來供讀者參考，最終是否要採用還是以 個人/團隊 的專案一致性與習慣為主。

個人認為，Coding Style 最重要的目標在於增加程式的 **易讀性** 與 **強健性**。只要符合這兩點，不但可以增加專案的開發速度、甚至也能增強程式的可維護性。



## 命名

講到 Coding Style 最重要的當然是命名了，因為命名是最直接影響到程式易讀性要素：

### 有意義的命名

優良的命名就像是用程式碼說故事，因為易讀性佳，也可以大大減少開發者撰寫無用註解的時間。

相較之下，額外的差勁的命名會使閱讀者無法看出程式碼的意義

* **壞例子：**

  ```javascript
  var a = 5,
  	b = 10;
  console.log(a + b);
  ```

  可以看到光看 `a`、`b`、`c` 我們根本不知道他是要做什麼的，乍看之下，我們甚至連每個變數的型態是甚麼都不知道。

* **好例子：**

  ```javascript
  var teaPrice = 5,
  	sandWichPrice = 10;
  console.log(teaPrice + sandWichPrice);
  ```

  相較之下，這個範例中一眼就可以看出情境是要映出早餐的總價錢。

### 命名風格

大部分命名我們都習慣使用 [駝峰式命名 ( Camel Case )](https://zh.wikipedia.org/wiki/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)

命名又可以區分為以下幾種：

#### 變數/函式 命名

採用小駝峰命名，例如：

```javascript
var lowerCamelCase;
function myFunction() {}
```

#### 私有變數/函式命名

前面加 `_` 代表私有變數

編著私有變數尤其重要，因為 JavaScript 並不是有所謂的 `private` 變數的語言，因此某些狀況下 ( 例如用 prototype 寫物件導向程式時 )，很多不公開的函式會因此項限制而被迫公開，此時標註私有變數警示開發者不要使用該私有變數就會是很重要的事情。請注意，不論是在函式上或者屬性上都可以使用這個命名方法。

範例如下：

```javascript
var myObj = {
  prop: '',
  _privateProp: 'private'
};
function _myPrivatefunction() {
  // ...
}
```

#### 常數命名

常數，可用全大寫命名，斷字間以 `_` 連接

由於 ES5 之前並沒有 `const` 變數，因此可以用這種方法來區別一個變數是否為常數：

```javascript
const PI = 3.14;
var MAX_VALUE = 100;
```



## 排版

### `{` 須加在該當行之後

這點除了保持程式的一致性以外，還可以避免不預期的錯誤，因為 JavaScript 會在 `return` 的該行最後加上補齊 `;`。

* **壞例子：**

  ```javascript
  function getBreakfast() 
  {
    return
    {
      tea: 'tea',
      sandWich: 'sandWich'
    };
  }
  console.log(getBreakfast());		// undefined
  ```

  如範例所示，JavaScript 自動在 `return` 行末加上了 `;`，因此答案為 `undefined`，在 `return` 行後的程式都不會跑到了。


* **好例子：**

  ```javascript
  function getBreakfast() {
    return {
      tea: 'tea',
      sandWich: 'sandWich'
    };
  }
  console.log(getBreakfast());		// {tea: "tea", sandWich: "sandWich"}
  ```

### `;` 規則

JavaScript 中有自動補齊功能，但這規則可能會跟我們預期的不同。

現在有兩派建議：

* 加分號派：加分號可以有效增加程式的強健性，避免未預期的錯誤發生


* 不加分號派：不加分號可以讓程式更簡潔，以增加易讀性

如果你是 不加分號派的 應該要更了解 JavaScipt 自動補完分號的規則，可參考[這篇](https://segmentfault.com/a/1190000004548664)

* 未預期的自動補完

  ```javascript
  var myNumber = 1
  (function() {
    console.log(myNumber)			// Uncaught TypeError: 1 is not a function
  })
  ```

* 使用 `;`

  ```javascript
  var myNumber = 1;
  (function() {
    console.log(myNumber)			// Uncaught TypeError: 1 is not a function
  });
  ```




## 嚴格模式

### 盡量使用 嚴格模式

使用嚴格模式可以增加程式的強健性，避免開法者做一些容易導致錯誤的操作。

### 避免全域使用 嚴格模式

雖然應該盡量使用 嚴格模式，但我們仍因該要避免對全域使用嚴格模式，因為當我們 include 的函式庫並不是 `'use strict'`，而我們卻在全域使用嚴格模式，就有可能產生大問題。

此時你可能會想，要如何不在全域使用嚴格模式呢，不是一宣告就整個程式都使用了嗎？其實很簡單，因為 `'use strict'` 與變數一樣是依照 Scope 決定影響範圍的，因此我們可以把每個獨立的檔案都用 IIFE 包起來，並在 IIFE 的最開頭放上 `'use strict'` 即可。範例如下：

```javascript
// one of my file
(function(){
  	'use strict';
	// all codes in this file
});
```

### 保持 嚴格模式 的一致性

即便今天因為某種原因而無法使用 嚴格模式，我們應該也要保持整個專案的嚴格模式一致性，應避免某些檔案使用嚴格模式，某些檔案又不使用嚴格模式，這樣會造成行為的不一致性，大大增加開發與除錯的難度。



## 宣告

### 記得要宣告變數

不論今天是使用 `var`、`let`，都沒關係，只要不要忘了加就好，因為忘了加這個屬性就會變成全域變數，會增加維護的難度，易讀性也會因為接手者搞不清楚這個變數是在哪宣告的而降低。

```javascript
function myFn() {
	inner = 'inner';  
}
console.log(inner);
```

### 宣告變數盡量加上初始值

這個規則不見得適合套用在每個情境上，但當我們的變數會是一個固定型別的時候，應該就要盡量給他一個初始值，如此可以有效的增加易讀性，因為接手者可以很快速的了解這個變數的應有型態。範例如下：

```javascript
var price = 0,
	myBreakfast = {};
```

### 將變數宣告在 Function 最上方

這點是因應 [JavaScript Hoisting](https://ithelp.ithome.com.tw/articles/10195005) 的對策，可以有效避免人腦 Hoisting 失敗產生不必要的錯誤

- **壞例子：**

  ```javascript
  var myString = 'hello world';
  function myFn() {
      console.log(myString);			// undefiend
  	var myString = 'hi world';
  }
  myFn();
  ```

  可以看到因為 `myString` 被提到了 `myFn` 的最前面，並遮蔽了外部 `myString` ，因此印出來的會是 `undefined`。

  為了避免這種意外發生，我們應該盡量將變數宣告在最上方：

- **好例子：**

  ```javascript
  var myString = 'hello world';
  function myFn() {
  	var myString = 'hi world';
      console.log(myString);			// hi world
  }
  myFn();
  ```

### 屬性應該盡量集中宣告

在容許的狀況下，應該要盡量集中宣告同個物件的屬性，否則可能會讓接手者無法一眼看清物件的結構。

當然在某些狀況下並不適合這種作法，但是狀況時就應該盡量實踐這個建議，例如：

```javascript
var image1 = {
  full: '/my/path/to/full/image',
  thumb: '/my/path/to/full/image'
}

var image2 = {
  full: '/my/path/to/full/image'
}

image1.thumb = getThumbPath(image1.full);
image2.thumb = getThumbPath(image2.full);
```

可以看到，`image1` 比起 `image2` 更可以讓開發者了解物件的全貌，如果宣告時有些屬性尚未要使用的話，可以先給他一個預設值，甚至是直接保持 `undefined` 也是一個做法。



## 小結

本篇中介紹了一些常見的 Coding style，Coding Style 的住要目的在於增加程式的 **易讀性** 與 **強健性**。只要符合這兩點，不但可以增加專案的開發速度、甚至也能增強程式的可維護性。

有些 Coding Style 比較主觀，最終是否要採用還是以 個人/團隊 的專案一致性與習慣為主。



## 參考

[飛肯設計: 進階 JS 班](http://www.flycan.com.tw/course/course-javascript-adv.php)

http://airbnb.io/javascript/
