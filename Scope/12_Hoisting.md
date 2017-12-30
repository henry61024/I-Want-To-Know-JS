# JavaScript Hoisting

如果你剛從 `C++` 或 `Java` 等語言轉來寫 `JavaScript` 的話，那你可能又要再次為 `JavaScript` 的行為感到詫異了。

在我們的認知中，程式就是一行一行執行下來的，但是 JavaScript 的一切都是這樣嗎？讓我們繼續看下去。



## Hoisting ( 拉升 )

***JavaScript 會把所有的宣告般拉升到當前 Scope 的最上面***

為了清楚了解 JavaScript 行為，當然要二話不說地開啟空白頁測試。

以下的程式，讓我們來猜猜第一個 `console.log(myString)` 會印出什麼？

```javascript
var myString = 'outer';
(function testHoisting() {
  console.log(myString);			// undefined
  var myString = 'inner';
  console.log(myString);			// inner
})();
console.log(myString);				// outer
```

照著程式就是一行一行的這個觀念，在 `console.log` 時，`myString` 因為尚未宣告，再根據 [Scope Lookup](https://ithelp.ithome.com.tw/articles/10194869) 的規則來看，JavaScript 應該會往上一層的 Global Scope 去查找 `myString` 這個變數，所以答案應該是 `"outer"` 才對。

這也許跌破了你的眼鏡，但實際上的答案卻是 `undefined`，為什麼呢？

### 宣告被拉升到當前 Scope 的最上面

#### 推論

利用目前學到的知識，讓我們來推斷一下這是哪來的 `undefined`。

首先，這個 `undefined` 絕對不會是外層 `myString` 造成的，因為外層 `myString` 的值已經被指派為 `'outer'` 了。

那麼會是因為我們尚未宣告就對 `myString` 取值，造成 JavaScript 在 Global 建立一個全新且未初始化的 `window.myString` 屬性的嗎？也不是，因為外層已經有一個 `myString` 了，所以 JavaScript 一定找得到名為 `myString` 的變數，自然也就不會建立一個新的 Global 屬性了。

#### 實際發生的事

那麼剩下的可能還有一種，這個 `undefined` 是 `testHoisting` 內 `myString` 的值，`var myString;` 這個宣告被單獨拉升到了目前所在 Scope 的最上層，而 `myString = 'inner';` 則被留在原地，因此產生了 `undefined`。

因此，我們應該把 `var myString = 'inner';` 拆成：`var myString;` 與 `myString = 'inner'` 看待，也就是說範例中的程式的執行流程應該是：

```javascript
var myString = 'outer';
(function testHoisting() {
  var myString;
  console.log(myString);			// undefined
  myString = 'inner';
  console.log(myString);			// inner
})();
console.log(myString);				// outer
```

這麼一來，一切都解釋得通了，`myString` 的宣告，被提到了最上面，緊接著又對他取值，此時取值的結果當然會是 `undefined`。JavaScript 的宣告並不是照著行數順序執行，而是被提到 Scope 的最上面預先執行了。

在這裡，要記住的是：**只有宣告會被拉升到當前 Scope 的最上面**，而同一個述句中初始化的動作會被留在原來的執行位置。

### 函式拉升

還記得函式跟物件享有相似的特性/待遇嗎？同樣的，函式也會被拉升：

```javascript
(function hoistFunction() {
	a();			// declaration
	var a = function() {
      console.log('expression');
    }
	function a() {
      console.log('declaration');
    }
    a();			// expression
})();
```

可以看到，`function a()` 這個宣告被拉到 Scope 的最上面，因此在還沒宣告的地方就執行 `a`，才不會跳出錯誤訊息，因此整個程式可以把它看作這樣：

```javascript
(function hoistFunction() {
	function a() {
      console.log('declaration');
    }
	a();			// declaration
	a = function() {
      console.log('expression');
    }
    a();			// expression
})();
```

`function a()` 被拉至最上面，而因為這就等於了 `a` 的宣告，因此 `var a` 就被省略了，理所當然的，第一個`a()`就會印出 `"declaration"`。接著執行到 `a = function(){}`，`a` 的值指向了新的函式，因此在第二個 `a()` 的地方才會印出 `"expression"`。



## 為何會有 Hoisting？

你一定會很好奇為什麼會有 Hoisting 這個行為，這時就要提到 JavaScript 中的 **Compiler ( 編譯器 )** 和 **Engine ( 引擎 )** 了。

### 變數宣告 屬於 Compiler 的工作

JavaScript Compiler 會將程式碼解析成有意義的語法結構，轉換成機器能夠讀懂的目的碼。接著 Compiler 會建立出所有的變數，保留其記憶體空間，並且繫結到所屬的 Scope 上。也就是說，**早在程式開始一行一行執行前，變數就已經被宣告好了**。

### 初始化/賦值 屬於 Engine 的工作

在 Compiler 編譯好程式之後，Engine 才會開始一行一行的執行程式。也就是說，初始化/賦值 的這個動作也是由 Engine 在編譯好之後，等待被執行的程式碼之一。

### 結果

* **Compiler 早在程式開始一行一行執行前，就已經把變數宣告好的這件事情**
* **初始化/賦值 也是由 Engine 負責的這件事情結合起來** 

**這兩件事情結合起來就會有宣告被拉升的錯覺**，因此我們才能把 `var myString = 'inner';` 拆開看成 `var myString;` 在 Scope 最頂端宣告，而 `myString = inner;` 則留在原地被執行。 



## 最佳實踐

* **宣告變數應該集中在當前 Scope 的最上面**

  再次的，這依然是為了提高易讀性而做的建議，試想每次在寫程式時，都還要先清楚地把 Hoisting 動作在腦內演練一輪，豈不是很累嗎？

  將變數集中宣告在最上面不但可以省去腦內 Hoisting，還可以有效降低寫錯的機率。



## 小結

JavaScript 有 Hoisting 的特性。何謂 Hoisting 呢？

Hoisting 就是 **把所有的宣告般拉升到當前 Scope 的最上面** 的特性，其中，我們必須注意幾點：

* **只有宣告會被拉升到當前 Scope 的最上面**
* **函式也會被拉升**

那為何會有 Hoisting 這項特性呢？因為

* **變數宣告 屬於 Compiler 的工作**
* **初始化/賦值 屬於 Engine 的工作**

這兩者合併起來就會有宣告被拉升的錯覺。

為了增加易讀性以及想錯 Hoisting 的步驟造成執行錯誤，我們**應盡量把變數宣告集中在當前 Scope 的最上面**。



## 參考

[You Don't Know JS: Scope & Closure](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)