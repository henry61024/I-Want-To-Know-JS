# JavaScript Coding Style 2

上一篇中，我們主要專注在討論偏向命名、排版等大多數與易讀性直接相關的議題。

這篇中，我則想要分享的更偏向一些讓程式更為簡潔的小技巧。相較於上一篇，今天要講的許多內容就更為主觀，最後的使用權與判斷還是交由你來決定。



## Scope 管理

首先我們還是先從一個 必須遵守的 Coding Style 開始講，這邊主要是複習之前的 [Scope 實作建議](https://ithelp.ithome.com.tw/articles/10195720)。

### 最小暴露原則

其實控管 Scope 最重要的一點就是要遵守最小暴露原則。如同物件導向的封裝，我們必須要隱藏外部不需知道的細節。封裝有幾個好處：

* 避免開發者動用到內部的函式、變數，而搞壞程式
* 讓你公開的 API 更加簡潔明瞭

#### 在一般 Function 中實踐

良好的封裝可以 **大大的增加** 可維護性，而封裝這個概念在每個函式中都是可以實踐的，只要把外部用不到的變數放在內部函式中即可

假設我們今天有一個變數 `onlyForMyFn` 只有在 `myFn` 才會使用到，例如：

```javascript
var onlyForMyFn = 'only for inner function';
function myFn() {
    // do something to 'onlyForMyFn'
}
// other codes ...
```

那應該要改成這樣更為適當：

```javascript
function myFn() {
    var onlyForMyFn = 'only for inner function';
    // do something to 'onlyForMyFn'
}
// other codes ...
```

如此外部就無法動用到 `onlyForMyFn` 這個變數，這樣可以有效減少 `myFn` 執行錯誤的機率。

#### 對檔案層級實踐 (利用模組)

在 JavaScript 中，一個很好的封裝方法就是利用 IIFE 包裹你的檔案，並只回傳要公開的 API 就好。如下：

```javascript
var myLibrary = (function() {
  var interface = {
        api1: api1,
        api2: api2,
    	...
      };
  function api1(name) {
	  // ...
  }
  function api2(name) {
	  // ...
  }
  // more codes ...
  return interface;
})();
```

可以看到，藉由這種模組化的做法，我們將整個要公開的 API 都濃縮在一個物件裡，因此我們只會在全域多新增一個物件而已。比起不用 IIFE 包裹，並且讓 API 散落在全域中，模組化會是一個好很多的選擇。



## 善用 Truthy & Falsy



## 簡潔程式

我認為簡潔程式與易讀性有很大的關連。當我們可以把程式縮減的比較短，同時也能保持程式一目了然的話，讀起來不是會更舒服嗎？

### 拒絕匿名 callback 強迫症

什麼是匿名 callback 強迫症呢？讓我們看看範例：

* **壞例子：**

  ```javascript
  setTimeout(function() {
  	wakeUp();
  }, 100);

  function wakeUp() {
    console.log('time to wakeup');
  }
  ```

  有沒有發現哪邊怪怪的呢？既然我們在匿名函式中只有要執行 `wakeUp`，那為什麼不乾脆傳入 `wakeUp` 就好了呢？

* **好例子：**

  ```javascript
  setTimeout(wakeUp, 100);

  function wakeUp() {
    console.log('time to wakeup');
  }
  ```

  這樣我們可以很快地減少兩行完全不必要且不利於閱讀的程式碼。現在，整個程式頓時清爽了許多。

  拒絕匿名 callback 強迫症是你我都該做的事情。

### 善用三元運算式 (Ternay Operators)

相較於拒絕無用的匿名 callback，善用三元運算式這點就是個比較主觀的意見。

```

```



### 善用各種 Operator



### `if return` vs `if {}` 

同樣是另一個主觀的意見，但個人會喜歡拿 `if return` 來取代 `if {}`。

這句話是什麼意思呢？首先讓我們來看看範例，現在我們要檢查 `input` 是否為 `undefined`，如果不是 `undefined` 的話我們才要接著做事。很直覺的作法會如下：

* **使用 `if {}`**

  ```javascript
  function myFn(input) {
  	if (input) {
        // do something
  	}
  }
  ```

  相較於這種風格，我會更喜歡以下這種寫法：

* **使用 `if return` ( 較推薦 )**

  ```javascript
  function myFn(input) {
  	if (!input) return;
      // do something
  }
  ```

  這種寫法中言簡意賅的表示，如果 input 是 `undefined` 等 falsy 值，就可以直接略過這個函式不看了。

相較之下，使用第一個寫法的話，在 trace 程式碼時你還要確定 `if` 結束的 `}` 位置是不是真的在函式的最下方，畢竟有可能在 `}` 之後還有做其他的事。

還有另外一點是，`if return` 可以讓程式的排版變得更扁平化。舉例來說，假如今天這個函式包含了很多其他的邏輯，那你的函式可能就會有變成 *波動拳* 的趨勢了：

* **使用 `if {}`**

  ```javascript
  function myFn(input) {
  	if (input) {
          if (...) {
              // do something
          }
  	}
  }
  ```

  那讓我們採用 `return` 寫法來看看：

* **使用 `if return` ( 較推薦 )**

  ```javascript
  function myFn(input) {
  	if (!input) return;
      if (...) {
          // do something
      }
  }
  ```

  這樣是不是清爽很多呢？