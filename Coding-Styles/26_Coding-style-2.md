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

### 善用 Truthy & Falsy

這點曾在 [原始型別篇](https://ithelp.ithome.com.tw/articles/10193371) 提過。如果今天我們要判斷一個值是否為 `undefined` 或是 `''`，依照其他語言的習慣我們可以這樣寫：

- **不使用 Truthy & Falsy**

  ```javascript
  if ((value === undefined) && (value === '')) {
  	// do something
  }
  ```

  那不如就簡化成：

- **使用 Truthy & Falsy ( 較建議 )**

  ```javascript
  if (!value) {
  	// do something
  }
  ```

  會簡潔許多

  又或者說要回傳一個 boolean 值

- **不使用 Truthy & Falsy**

  ```javascript
  function returnBoolean() {
    	// ..
  	return Boolean(value1) && Boolean(value2);
  }
  ```

  那不如這樣簡化

- **使用 Truthy & Falsy ( 較建議 )**

  ```javascript
  function returnBoolean() {
    	// ..
  	return !!value1 && !!value2;
  }
  ```

  利用 Truthy & Falsy 可以有效提高易讀性。

### 善用三元運算式 (Ternay Operators)

善用三元運算式 可以讓程式更為簡潔。

假設現在我們要決定圖片的高度與寬度，如果大於 200 就設為兩百：

* **使用 `if`：**

  ```javascript
  function myFn(height, width) {
    if (height > 200) {
      height = 200;
    }
    if (width > 200) {
      width = 200;
    }
    // do something
  }
  ```

  這會是一般的寫法，如果使用三元運算式的話，我們可以將程式簡化成這樣：

* **使用 三元運算式 ( 較推薦 )**

  ```javascript
  function myFn(height, width) {
    height = (height > 200) ? 200 : height;
    width = (width > 200) ? 200 : width;
    // do something
  }
  ```

  使用三元運算式可以讓程式更為簡潔，除此之外，閱讀者也可以很清楚的知道你的意圖就是要賦值 ( 因為開頭就是 `變數 = ... ` )。

  更進一步，我們還可以把 三元運算式 運用在決定函式上。

  舉個例子來說，假設我們今天依照想不想睡來決定要做什麼事情，我們可能會這樣寫

* **使用 `if`：**

  ```javascript
  function doSomething(isSleepy) {
    if (isSleepy) {
      drinkCoffee();
    } else {
      startCoding();
    }
  }

  function drinkCoffee() {...} 
  function startCoding() {...} 
  ```

  如果使用三元運算式的話：

* **使用 三元運算式**

  ```javascript
  function doSomething(isSleepy) {
    var decision = isSleepy ? drinkCoffee : startCoding;
    decision();
  }

  function drinkCoffee() {...} 
  function startCoding() {...} 
  ```

  這樣也不失為一種做法。當然，相較於使用三元運算式還單純賦值，使用三元運算式來決定要使用哪個函式可能就更為主觀了！

### 善用各種 Operator

與善用三元運算式一樣，Operator 會是簡潔程式碼強大的工具。

讓我們把剛剛圖片寬高的需求稍作修改，假設我們的寬與高是 `undefined` 的話，就把他們設為 200，否則保持原值。

* **不使用 Operator：**

  ```javascript
  function myFn(height, width) {
    if (!height) {
      height = 200;
    }
    if (!width) {
      width = 200;
    }
    // do something
  }
  ```


* **使用 Operator ( 較建議 )：**

  ```javascript
  function myFn(input) {
    height = height || 200;
    width = width || 200;
    // do something
  }
  ```

  可以看到，在這個範例中，相較於第一個作法，使用 `||` Operator 會更壓倒性的易讀。

  在 JavaScript 中還有許多的 Operator，在這邊就不再贅述，但我們要知道如果對於各種 Operator 都有更深的了解，也許就會發現新的一片天。

### `if return` vs `if {}` 

同樣是另一個主觀的意見，如果我們的 `if` 是要拿來做一些是否要接續執行函式的判斷，那麼可以用 `if return` 來取代 `if {}`。

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




## 小結

這篇中介紹了一些偏向小技巧的 Coding Style。

有注意到嗎，其實 Coding Style 就像是對於程式的執著與熱誠，只要在小細節多思考、多留心，就能夠做出巨大的改變，不但能寫出優雅許多的程式，我們也會因為這些執著與經驗的累積而變成更好的程式設計師。



## 參考資料

工作上的小經驗 與 前輩們的指導






## Best Practice

- 不要用 `typeof null === "object"`
- `argument.length` when parameter is optional


- `&&` ???

  當 `&&` 出現時， JavaScript 會把 左運算元 跟 右運算元 分別強制轉型後再做 AND 的動作

  ```
  console.log( 'hello world' && undefined );		//false
  ```

- `||`

  與 `&&` 概念相同

  ```

  ```

  ​

- `? :` choose function, value

- truthy falsy

- `setTimeout(function(){})`、`bind`

- `!!`

- `if {}` 要 `{}`


- 可將 parameter設成optional，function內部搭配 `!!` 使用 ( `arguments.length` )


- 常見錯誤，例如 undefined
- 跟隨 team 的 coding style => 跟隨大專案的 coding style
- **專案一致性**



- **不要使用 delete 来删除一个数组中的项**
- length = 0