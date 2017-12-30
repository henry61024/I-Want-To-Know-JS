# JavaScript Scope 單位 & Scope Lookup

在上一篇提到，Scope 分為 Lexical Scope 與 Dynamic Scope 兩種，而 JavaScript 採用的是 Lexical Scope。

Lexical Scope 代表區塊間的關係，**被包裹在內層的區塊可以保護自己的變數不被外層取用，相反的外層區塊的變數還是可以被內層區塊使用**。

在這一節中，我們要更深入的討論 JavaScript 實際上是如何利用 Scope 來查找變數的。

但在此之前，我們要先了解 JavaScript 能夠形成 Scope 的基本單位有哪些。



## JavaScript 的 Scope 單位

- **Function 是 JavaScript 中最常見的 Scope 單位**

   如果曾經學習過 `C++` 與 `Java` 等語言，你會知道在這些語言中，例如 `if() {}`、`for() {}` 等 `{}` 包裹的區塊是會產生一層 Scope 的，但很不幸的，JavaScipt 會為 Function 產生 Scope，而不是為每個 `{}` 都產生。

  先讓我們複習一下 **JavaScript Function 的 Scope**：

  ```javascript
  function myFunction() {
    var inner = 'This is myFunction';
  }
  console.log(inner);		// Uncaught ReferenceError: inner is not defined
  ```

  如我們所料，`a` 是在 `myfunction`包裹起來的 Scope 中宣告的，因此 Global Scope 是拿不到 `a` 的。

  輪到 **測試看看 `if() {}` 區塊是否會產生 Scope：**

  ```javascript
  function myFunction() {
    if (true) {
      var insideIf = 'This is inside if';
    }
    console.log(insideIf);		// "This is inside if"
  }

  myFunction();
  ```

  今天我們很上進的想要保護變數 `b` 不讓外層 Scope 使用，但是 oops，你可能要失望了，不只是 `if` 區塊內，整個 `myFunction` 都可以拿的到變數 `b`。

  不信邪的再來 **測試看看 `for() {}` 是否能產生 Scope：**

  ```javascript
  function myFunction() {
    for(var i = 0; i < 5; i++) {
      var insideFor = 'This is inside for';
    }
    console.log(insideFor);		// "This is inside for"
    console.log(i);				// 5
  }

  myFunction();
  ```

  果然，`insideFor` 在 `myFunction` 整個區塊內都是拿的到的，但是讓人驚訝的是，只是用來做計數器的 `i` 竟然也可以整個 `myFunction` 取用。

  從上面的說明可以確定 **Function 為 JavaScript 中最常見的 Scope 單位，而非 `{}` 組成的區塊。**

- **`catch`、`with`也可以產生 Scope**

  在 [You don't know JS: Scope](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures) 中提到，除了 Function 外，JavaScript 確實還是有些東西可以產生 Scope，例如 `try/catch` 中的 `catch` 還有 `with`，在這邊就不再贅述。

- **ES6：`let`、`const` 會把區塊當作 Scope**

  除此之外，ES6 的 `let` 與 `const` 則是在宣告變數/常數時可以綁定 區塊，達成一般 區塊 Scope 的作用。這部分會在 ES6 篇幅裡詳細介紹。





## JavaScript Scope Lookup ( 查找 )

Lexical Scope 的規則是 **外層 Scope 無法取用內層變數，但內層 Scope 可以取用外層變數**。JavaScript 也是如此，但 JavaScript 是怎麼查找變數的，才能符合 Lexical Scope 的規則呢？

簡單來講就是：**由當下的 Scope 往外層 Scope 查找，一旦找到就停止**。

因此，如果一直沒有找到變數的話，**JavaScript 就會由當下的 Scope 一路查找到最外層的 Global Scope**。

但這個規則會衍生兩個問題：

### 直到 Global Scope 都沒有找到該變數

第一個問題：如果從當下的 Scope 一路往上找，找到 Global Scope，JavaScript 都沒看到該變數的話怎麼辦？

這時我們要再細分一下情境，現在我們查找這個變數，是為了要 **取值 or 賦值** 呢？

* **如果是取值，就會發出 Reference Error：**

  讓我們來看看範例，如果我們讓 Scope 試圖去取出一個不存在的 `notDeclaredVar` 變數的值會怎樣？

  ```javascript
  function logNonExist() {
    console.log( notDeclaredVar );		
  }
  logNonExist();		// Uncaught ReferenceError: notDeclaredVar is not defined
  ```

  Boom！，找不到這個變數！

  因此如果是 取值 卻找不到該變數的話，JavaScript 就會發出 `Uncaught ReferenceError: notDeclaredVar is not defined` 的錯誤，甚至不需要加上 `'use strict'`，畢竟要怎麼取出一個不存在變數的值呢？

* **如果是賦值，就會新建一個變數 or 發出錯誤：**

  接著下個範例，如果我們讓 Scope 試圖去賦值給一個不存在的 `notDeclaredVar` 變數會怎樣？

  ```javascript
  function assignToNonExist() {
    notDeclaredVar = 'I am is not Declared';
  }
  assignToNonExist();
  console.log(notDeclaredVar);				// "I am is not Declared"
  console.log(window.notDeclaredVar);			// "I am is not Declared"
  ```

  竟然 log 的出來！

  因此如果是 賦值 卻找不到變數的話，JavaScript 就會一路查找到 Global Scope，發現連 Global Scope 都沒有的時候，[就會建立一個新的 Global 屬性](https://ithelp.ithome.com.tw/articles/10193821)。

  那如果是在 `'use strict'` 下呢？

  ```javascript
  'use strict';
  function assignToNonExist() {
    notDeclaredVar = 'I am is not Declared';	// Uncaught ReferenceError: notDeclaredVar is not defined
  }
  assignToNonExist();
  console.log(notDeclaredVar);
  console.log(window.notDeclaredVar);
  ```

  不用等到 `console.log` 的地方，在賦值給 `notDeclaredVar` 時就爆掉了。

  因此在 `'use strict'` 下，JavaScript 也是不接受對不存在變數賦值的。

### Shadowing ( 遮蔽 )

第二個問題：如果我們同時有兩個同名的變數宣告在不同層的 Scope 會怎樣呢？以下我們將用這個範例來講解 Shadowing ( 遮蔽 )：

```javascript
var myString = 'hello global';
function testShadowing() {
  var myString = 'hello scope';
  console.log(myString);			//  "hello scope"
}
testShadowing();
console.log(myString);				// "hello global"
```

首先我們要確定的是外層 `myString` 與 內層 `myString` 到底是不是同一個變數

* **在不同 Scope 可以再宣告同名變數，而這變數並不是原本的變數：**

  讓我們來看看範例，我們在 Global Scope 中宣告了一個 `myString` 。執行 testShadowing 時，我們又重新宣告了一個 `myString`，而在 Function 中， `myString` 的內容會是 `"hello scope"`。到這裡為止，你可能會認為這樣無法證明他 到底是真的又宣告了一個新的 `myString` 變數 又或者我們只是對原來的 `myString` 賦值而已。讓我們繼續看下去。

  當執行完 `testShadowing` 後，再印出一次 `myString`，發現他的內容是 `"hello global"`。按照 [JavaScript Call by sharing](https://ithelp.ithome.com.tw/articles/10194299) 的概念來說，如果我們在 `var myString = 'hello scope';` 時只是對外層的 `myString` 賦值的話，那我們在外層 `console.log(myString)` 時應該也會得到 `"hello scope"`。因此可以得知，在 `testShadowing` 內，我們確實是新增了一個新的 `myString` 變數。

有這個前提後，就可以輪到 Scope 上場了

* **內層 Scope 中的變數會 Shadow ( 遮蔽 ) 外層變的同名變數**

  在 `testShadowing` 中的 `myString` 是在內層 Scope 宣告的。當我們在 Function 內 `console.log(myString)` 試圖取內層 `myString` 的值時， JavaScript Scope 就遵循 **由內層 Scope 往外層 Scope 找，找到就停止** 的規則，馬上的，找到自己的 Scope 內有一個 `myString`，就認為已經找到要的變數於是停止查找，並且把值印出來，而外層的 `myString` 理所當然的就被 Shadow ( 遮蔽 ) 了。

從上面的說明可以知道，因為

1. JavaScript 可以重複宣告變數
2. JavaScript Scope 是由內而外查找的

因此才會有 Shadowing 的特性。



## 注意事項

另外，還有一些注意事項：

* **屬性存取是藉由屬性存取規則去查找的，這並不是 Scope 的工作**，這之後會在 Prototype 篇章中涵蓋。

* **JavaScript 中，是有方法能夠脫離 `Lexical Scope` 的**，最常見的方法就是 `eval`。

  `eval` 可以輸入字串，編譯器會在執行時期時把他轉成程式碼，也就是說他脫離了 Lexical Scope 是在編譯時期就決定好 Scope 的概念。

  如果要更深入了解 `eval`，可以參考：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval



## 實作建議

這邊要提兩點：

* **雖然有 Shadowing 這個特性，但還是不要重複宣告同名變數**

  宣告同名變數，會造成語意不清，難以維護。

* **盡量別用 `eval`**

  使用 `eval`，不但會造成程式碼難以 Debug，也會造成效能低落，因為編譯器無法預先知要執行的程式是甚麼，自然也就無法最佳化 `eval` 述句與相關程式碼的效能。



## 小結

**JavaScript 中，最常見能產生 Scope 的單位是 Function，而不是區塊。**

基本上，JavaScript 的查找規則跟一般的 Lexical Scope是一樣的，但講詳細一點的話就是 **由當下的 Scope 往外層 Scope 查找，一旦找到就停止**。這個規則有**兩個額外需要補充的**，就是：

* **如果直到 Global Scope 都沒有找到該變數**
  * 如果是要對這個不存在變數取值，就會發出 Reference Error
  * 如果是要對這個不存在變數賦值
    * 如果是非嚴格模式，JavaScript 會為你建立一個新的 Global 屬性
    * 如果是嚴格模式，會發出 Reference Error 的錯誤
* **如果內層 Scope 跟外層 Scope 有同名變數，則外層 Scope 的變數會被 Shadow ( 遮蔽 )**

除此之外，還有**兩點實作建議：**

- 雖然有 Shadowing 這個特性，但還是不要重複宣告同名變數，因為會造成語意不清
- 盡量別用 `eval`，會讓程式難以 Debug 外，還會造成效能低落



## 參考

[You Don't Know JS: Scope & Closure](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)