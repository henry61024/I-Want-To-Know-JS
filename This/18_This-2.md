# JavaScript This

先讓我們前情提要一下，JavaScript 中的 **this 取決於 Function 的執行情境與方式**，而在 You don't know JS 中，詳細的說明了在執行了 Function後，JavaScript this 的五種繫結：

- 預設繫結
- 隱含繫結
- 明確繫結
- new 繫結
- 語彙繫結

在[上一篇](https://ithelp.ithome.com.tw/articles/10196074)中，我們介紹了 預設繫結 與 隱含繫結，接著我們就要介紹剩下的三個繫結。



## 明確繫結

還記得我們[介紹過](https://ithelp.ithome.com.tw/articles/10195896)的 `call`、`apply`、`bind` 嗎，這三個 Function 函式的其中一個重要功能就是明確指定要執行 Function 中的 this 是什麼，因此他的套用情境很好辨別：

### 套用情境

- 當我們使用 `call`、`apply`、`bind`執行 Function 時，就會套用明確繫結

而 this 的內容也顯而易見

### this 的內容

- this 的內容就會是我們在 `call`、`apply`、`bind` 中指定的第一個參數

讓我們來看看範例：

### 範例

#### 使用 `call`、`apply`

```javascript
var myString = 'hello global',
	obj = { myString: 'hello object' };
function logMyString() {
  console.log(this.myString);
}
logMyString.myString = 'hello function';

logMyString();							// "hello global"
logMyString.call(obj);					// "hello Object"
logMyString.apply(obj);					// "hello Object"

logMyString.call(null);					// "hello global"
logMyString.apply(null);				// "hello global"
```

在這個範例中，我們分別用 `call` 與 `apply` 分別執行 `logMyString`，首先我們試著把 `obj` 作為第一個參數帶入，毫無意外的，我們可以看到答案皆為 `"hello Object"`。

在[上一篇](https://ithelp.ithome.com.tw/articles/10196074)中提到，當我們將 `null` 帶入 `call` 或 `apply` 的第一個參數時，會等於不套用指定任何物件為 this，也就代表*回到預設繫結*，就讓我們來測試看看。我們可以看到答案都是 `"hello global"`，也與我們預期的相同。

從這個範例中可以看到 `call` 跟 `apply` 在指定 this 的能力上是一模一樣的，也就是說，`call` 與 `apply` 的使用時機還是要回到要以何種方式將參數帶入目標函式中來決定。更詳細可以參考[這一篇](https://ithelp.ithome.com.tw/articles/10195896)。

#### 使用 `bind`

接下來輪到介紹 `bind`：

```javascript
var myString = 'hello global',
	obj1 = { myString: 'hello object1' },
    obj2 = { myString: 'hello object2' };
function logMyString() {
  console.log(this.myString);
}
logMyString.myString = 'hello function';

var bindedFn = logMyString.bind(obj1);
bindedFn();										// "hello object1"

bindedFn.call(obj2);							// "hello object1"

obj2.log = bindedFn;
obj2.log();										// "hello object1"
```

我們先對 `logMyString` 執行 `bind` 後將值賦予 `bindedFn`，緊接著執行這個包裹函式 `bindedFn()`。同樣的，因為我們明確的指定了 `obj1` 當作 this，因此答案自然也會是 `"hello object1"`。

既然都一樣的話，為何要把 `bind` 跟 `call`、`apply` 分開討論呢？

#### `bind` 的強制繫結

有沒有想過當我們對被 `bind` 包裹的函式在進行一次明確指定 this 的話會怎麼樣？看看 `bindedFn.call(obj2);` 的結果會是甚麼，出乎意料的，`bindedFn` 的 this 竟然沒有被指定回 `obj2`，而是依然保留在 `obj1`。為什麼呢？就來回顧一下我們自製的簡易 bind polyfill 吧：

```javascript
function bind(t, callback) {
  var outerArgs = Array.from(arguments).slice(2);
  return function() {
  	var innerArgs = Array.from(arguments);
    return callback.apply(t, outerArgs.concat(innerArgs));
  }
}

var bindedFn = bind(obj1, logMyString);
bindedFn();									// "hello object1"
bindedFn.call(obj2);						// "hello object1"
obj2.log = bindedFn;
obj2.log();									// "hello object1"
```

來看看這個 `bind` polyfill 是怎麼運作的。有注意到 `callback.apply(t, outerArgs.concat(innerArgs))` 的第一個參數 `t` 嗎？當我們執行 `bind` 後，會回傳一個匿名函式，而這個匿名函式再次被執行時，不論此時匿名函式中的 this 是甚麼，我們還是會把傳入 `bind` 的第一個參數明確指定給 `callback` 作為 this。再稍稍複習一下，這個匿名函式之所以還是能找到 `t` 並傳入 `callback` 還是多虧了 [Closure](https://ithelp.ithome.com.tw/articles/10195343) 的威能！

也就是說，此時不論是再使用 隱含繫結 或是 明確繫結 都是無法再影響傳入 `callback` 的 this 的，因此 `bindedFn.call(obj2);` 與 `obj2.log();` 的答案才依然會是 `"hello object1"`。



## new 繫結

在 [介紹 Function](https://ithelp.ithome.com.tw/articles/10194538) 時曾經說明過，`new` 也是執行 Function 的一種方式。與一般執行方式的最大差別在於 `new` 會把目標函式當作建構式使用。當作建構式的意思是以下幾樣事情會被執行：

* 首先會新建出一個物件
* 此新建物件的子型別被設為該建構式 (constructor)
* 建構式中的 this 會被繫結為此新建物件
* 回傳新建物件 ( 如果建構式本身沒有回傳東西的話 )

有了這些觀念，`new` 繫結的 套用情境 與 內容 就顯而易見了

### 套用情境

- 當我們使用 `new` 執行 Function 時，就會套用 new 繫結

### this 的內容

- this 的內容會是 `new` 新建出來的物件本身

  需要釐清的是，this 的內容會是新建出來的物件，而不是 Function 本身

### 範例

現在用 `new` 把 `logMyString` 當作一個建構式使用，來看看 this 會指向何處：

```javascript
var myString = 'hello global';
function logMyString() {
  this.myString = 'hello new object';
}
logMyString.myString = 'hello function';
var newObj = new logMyString();
console.log(newObj.myString);					// "hello new object"
```

在這個範例中，我們故意對 `logMyString` 這個函式本身也設定了一個 `myString` 屬性來確認 this 真正代表的位置。

在我們以 `new logMyString();` 執行 Function 後，就會隨即回傳一個被 `new` 出來的物件，我們把這個物件指派給 `newObj`。印出 `newObj.myString` 後會得到 `"hello new object"`。這可以驗證建構式中的 this 確實是 new 出來的新物件。



## 語彙繫結

最後的一項細節是語彙繫結，這是利用 ES6 的新語法 `() => {}` 箭號函式來實現。

箭號函式可以當作是 匿名函式 的語法糖來看，但扯到 this 時，就不只是這樣了。之所以稱做 "語彙" 繫結，就是因為箭號函式的 this 會被繫結到包裹他的 Function 的 this，這個概念跟其他語言的 this 很雷同了。因此我們的 套用情境 與 this 內容會如下：

### 套用情境

- 當我們使用箭號函式 `() => {}` 執行 Function 時，就會套用語彙繫結

### this 的內容

- 與包裹他的 Function 的 this 相同

接著來看看範例：

### 範例

```javascript
var myString = 'hello global',
    obj = {};
function outer() {
  this.myString = 'hello outer';
  return () => {
  	this.myString = 'hello arrow function';
  }
}

var arrowFn = outer.call(obj);
console.log(obj.myString);				// "hello outer"
arrowFn();
console.log(window.myString);			// "hello global"
console.log(obj.myString);				// "hello arrow function"
```

為了更清楚看到實際發生的狀況，我們先把 `outer` 的 this 繫結明確設為 `obj`，並用 `arrowFn` 這個變數接住回傳的箭號函式。此時印出 `obj.myString`，確認有將 `"hello outer"` 成功設進 `obj.myString` 中。

當我們執行 `arrowFn()` 時，按照之前的概念，因為此時符合了預設繫結的規則：使用 `()` 單獨呼叫函式，因此照道理匿名函式中的 this 的內容會是 global 物件，但事實上卻沒有，`window.myString` 依然是 `"hello global"`。

這時使用上述語彙繫結的規則來看看，沒錯，`arrowFn` 的 this 之後永遠都會是 `obj` 了，因此此時印出 `obj.myString` 的答案就會是 `"hello arrow function"`。

### 利用 Closure 實作語彙繫結

其實語彙繫結的功效即便不用 ES6 還是可以很容易地達成的，既然提到 語彙 ( Lexical Scope ) 自然就會 Closure 了。就讓我們再次借用 Closure 的威能吧！

在製作範例以前，先讓我們想想要怎麼樣才能 內部函式 永遠都可以拿到 外部函式的 this。明確的點出目標之後，其實答案就在不遠處了。想到了嗎？很簡單，我們只要把 this 指派給外層 Function 的一個變數，之後內層函式在使用這個變數的時候，就可以利用 Scope 查找到這個被存起來的外層 this 了！

就像這樣：

```javascript
var myString = 'hello global',
    obj = {};
function outer() {
  var self = this;
  self.myString = 'hello outer';
  return function() {
  	self.myString = 'hello anonymous function';
  }
}

var anonymousFn = outer.call(obj);
console.log(obj.myString);				// "hello outer"
anonymousFn();
console.log(window.myString);			// "hello global"
console.log(obj.myString);				// "hello anonymous function"
```

我們執行 `outer.call(obj)` 後，我們立即把 this 指派給新的變數 self，往後所有關於 this 的操作，我們都以 self 來替代。

當執行 `anonymousFn()` 時，首先就向 Scope 往上查找 `self`，馬上的我們就在 `outer` 的 Scope 中發現了他，而此時 self 的內容正是 `outer` 執行時被指派的 this：`obj`。同樣的，往後 `anonymousFn` 的 this 都會是 `obj` 了，因為我們不再直接對 this 做操作，而是用新變數 `self` 來代為操勞了。因此 `obj.myString` 的內容會是 `"hello arrow function"`。



## 小結

本篇中，我們介紹了剩下的三個繫結：

* **明確繫結：**

  * **套用情境：**

    當我們使用 `call`、`apply`、`bind`執行 Function 時，就會套用明確繫結

  * this 的內容：

    this 的內容會是我們在 `call`、`apply`、`bind` 中指定的第一個參數

* **new 繫結**

  * **套用情境：**

    當我們使用 `new` 執行 Function 時，就會套用 new 繫結

  * this 的內容：

    this 的內容會是 `new` 新建出來的物件本身

* **語彙繫結**

  - **套用情境：**

    當我們使用 ES6 的箭號函式 `() => {}` 執行 Function 時，就會套用語彙繫結

    除此之外我們也可以用 Closure 來達成這個效果。

  - **this 的內容：**

    與包裹他的 Function 的 this 相同




## 參考

[You Don't Know JS: this & object prototypes](https://github.com/getify/You-Dont-Know-JS/tree/master/this%20%26%20object%20prototypes)