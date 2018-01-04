# JavaScript call，apply，bind

`call`、`apply`、`bind` 三者都是 JavaScript Function 的內建函式，他們與 this 的關係重大，除此之外，`call` & `apply` 可以作為呼叫 Function 的另一個手段，而 `bind` 則會回傳一個經過包裹後的 Function 回來。

因為目前尚未介紹 JavaScript this 的關係，本篇中只會些微帶過 this，而集中注意力在 `call`、`apply`、`bind` 的其餘功能上。this 的詳細介紹以及他們與這三個內建函式的關係，將會在 this 篇介紹。



## call

`call` 是 Function 的內建函式，首先當然要了解 `call` 有什麼功能，是不是有什麼我們必須使用他的時刻。

### 功能

* 執行 function
* 明確指定 this

執行 function 這點，基本上只要用 `()` 就是可以取代的，然而我們並沒有辦法使用 `()` 來指定 this。

讓我們來看看 `call` 的使用方法

### 語法

`call` 的使用方法是 `fn.call(this, arg1, arg2..., argn)`。`call`主要有兩種參數：

* **第一個參數：**

  輸入的物件會被指定為目標函式中的 `this`

* **第二以後的參數：**

  會作為**參數傳進目標函式**中，如果目標函式中不需要參數則不要傳入即可

讓我們直接來看看範例：

### 範例

```javascript
function add(a, b) {
  return a + b;
}
console.log(add(1, 2));						// 3
console.log(add.call(null, 1, 2));			// 3
```

可以注意到，`add.call(null, 1, 2) ` 中，我們帶入的第一個參數是 `null`，這是因為我們的 `add` 函式不需要 `this`，因此我們可以使用 `null` 來忽略他。這種做法有可能會產生一些副作用，更詳細的討論將會放在 this 篇中。

接著來看 `add.call(null, 1, 2)` 的第二與第三的參數 `1` & `2`，他們相當於 `add(1, 2)` 中的`1` & `2`，會帶進 `add` 中當作參數。

在這個範例中，因為我們 `add` 函式中並沒有用到 `this`，因此 `add(1, 2)` 的結果會等價於 `add.call(null, 1, 2)`。

那到底在什麼狀況下我們才用的到 `call` 呢？

### 使用情境

我們知道執行 Function 的功能可以由 `()` 取代即可，在綜合上面的敘述，可以很清楚的知道 `call` 對我們來說唯一的**使用情境就在於要明確指定 `this` 的時候**。



## apply

基本上，`call` 跟 `apply` 就是個雙胞胎，他們的**執行後的功能一模一樣**，但因為參數不同，讓我們更多的情境會使用到 `apply`。就事不遲疑讓我們來介紹 `apply` 的語法。

### 語法

`apply` 的使用方法是 `fn.apply(this, [arg1, arg2..., argn])`。`apply`主要有兩個參數：

- **第一個參數：**

  輸入的物件會被指定為目標函式中的 `this`

- **第二個參數：**

  必須是**陣列**，會把**陣列中的每個元素作為參數傳進目標函式**中，如果目標函式中不需要參數則不要傳入即可

讓我們用跟 `call` 一樣的範例來做個比較：

### 範例

```javascript
function add(a, b) {
  return a + b;
}
console.log(add(1, 2));						// 3
console.log(add.call(null, 1, 2));			// 3
console.log(add.apply(null, [1, 2]));		// 3
console.log(add.apply(null, 1, 2));			// Uncaught TypeError: CreateListFromArrayLike called on non-object
```

觀察一下 `console.log(add.apply(null, [1, 2]));`。因為 `add` 不需要 `this`，同樣的我們帶入 `null` 來忽略this 即可。接下來看第二個參數 `[1, 2]`，這個陣列中的兩個元素就當於 `add(1, 2)` 中參數 `1` & `2`，同樣是要作為 `add` 的 `a` & `b` 傳入 `add` 中。

既然都說第二個參數必須是陣列，難道不會好奇第二個參數的值不傳入陣列會怎麼樣嗎？讓我們來看看 `console.log(add.apply(null, 1, 2));`，引擎毫不留情的吐出了一個錯誤訊息 `Uncaught TypeError: CreateListFromArrayLike called on non-object` 給我們。

那如同開頭介紹的，既然 `apply` 與 `call` 功能相同，那有什麼情境會是 `apply` 辦的到而 `call` 卻無法達成的呢？

### 使用情境

就讓我用個範例來說明：

如果今天我們的功能依然是要加總數字，但我們多了一個需求是要能夠把所有帶入參數相加的話，那我們要怎麼實做呢？

```javascript
var list = [1, 5, 8];
function add() {
  return Array.from(arguments).reduce(function(sum, num) {
    return sum + num;
  });
}
console.log(add.call(null, 1, 2));			// 3
console.log(add.apply(null, list));			// 14
```

先來補完幾個先前沒說明過的語法：

* **`arguments`：**

  **代表傳入目標 Function 的所有參數**，與 `apply` 的第二個參數雷同，這些參數都是**包裹在一個 類陣列 (array-like)** 裡，請注意這個類陣列並沒有 Array 的內建函式。詳見 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)。

* **`Array.from()`：**

  這個是 ES6 的語法，他的功能是 **把類陣列物件轉換成陣列**。詳見 [MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/from)。

* **`array.reduce()`：**

  這個是 Array 的內建函式，可以 **把陣列收束成單一值**。詳見 [W3Schools](https://www.w3schools.com/jsref/jsref_reduce.asp)。

接著就可以開始講解我們的範例了。參考以上三個補完語法的說明，我們可以知道 `add` 這個 Function 做的事情很簡單，就是把所有的傳入的參數全部加總起來然後回傳。

現在我們有一個陣列這時就發現，`call` 完全派不上用場，而使用 `apply`，並把 `list` 當作一個參數傳入就可以輕鬆完成 ( 當然你會發現 `list.reduce()` 就可以完成範例，但還是恕我用這個簡易的例子來講解，如果想看更實際的例子，可以看 `bind` 章節中的 `簡易 Pollyfill` )。

因此我們可以知道， **當我們的 Function 並沒有限定參數，又或者我們根本不知道參數的實際數量時就可以使用 `apply`** 。



## bind

如果我們說 `call` 跟 `apply` 是雙胞胎的話，那 `bind` 與 `call` 跟 `apply` 的關係就是表兄弟了吧！讓我們先看看 `bind` 的功能：

### 功能

* 明確指定 this
* 回傳一個包裹函式，當我們執行這個函式時，同時也會將帶入 bind 的 arguments 一起帶進 Function 中。類似 [Currying](https://jigsawye.gitbooks.io/mostly-adequate-guide/content/ch4.html) 的概念。

### 語法

`bind` 的使用方法是 `fn.bind(this, arg1, arg2..., argn)`。`bind`主要有兩種參數：

- **第一個參數：**

  輸入的物件會被指定為目標函式中的 `this` ( 以硬繫結的方式 )

- **第二以後的參數：**

  會作為**往後傳進目標函式的參數**，如果目標函式中不需要參數則不要傳入即可

- **回傳：**

  回傳包裹後的目標函式。執行這個包裹函式後，可以幾乎確定 this 不會被改變，另外，**也可以把先前傳入 `bind` 的參數 一並帶進目標函式中**

該是時候來看看這些表兄弟之間的差別了！

### 範例

今天假如我們要使用 `add` 把各種數字跟 `1` 相加的話，利用 `call`、`apply`、`bind` 要怎麼完成呢？

```javascript
function add(a, b) {
  return a + b;
}
add.call(null, 1, 2);			// 3
add.call(null, 1, 4);			// 5
add.apply(null, [1, 2]);		// 3
add.call(null, [1, 4]);			// 5
var add1 = add.bind(null, 1);
console.log(add1(2));			// 3
console.log(add1(4));			// 5
```

可以看到，`call` 跟 `apply` 的使用方式很基本，總之就是把 `1` 帶入其中一個參數就好。

接著來看看 `bind`。我們使用 `var add1 = add.bind(null, 1);`，這樣當之後我們執行 `add1` 的話，`bind` 的第二個參數`1` 就會作為 `add` 函式的第一個參數帶入了，也就是說，之後我們只需要帶入第二個參數給 `add1` 就可以達成 `add(1, ?)` 的功能了。

### 簡易  Polyfill

讓我們簡易實作 `bind` 的功能來更進一步了解他的運作方式：

```javascript
function bind(t, callback) {
  var outerArgs = Array.from(arguments).slice(2);
  return function() {
  	var innerArgs = Array.from(arguments);
    return callback.apply(t, outerArgs.concat(innerArgs));
  }
}
var addWithBind = bind(null, add, 1, 5);
console.log(addWithBind(8));				// 14
```

這邊將 `bind` 的介面設計成 `bind(t, callback, arg1, arg2, ..., argn)`。第一個參數依然是放 this，而第二個參數變成放 `callback`，後面的參數則依 `callback` 的需求帶入。

可以看到執行 `bind` 後會回傳一個匿名函式，當之後我們執行這個匿名函式時 ( e.g：`addWithBind` )，他就會把先前帶入 `bind` 的 `arg1 ~ argn` 與傳入匿名函式的所有 `arguments` 合併起來。因為我們並不知道 `callback` 會有幾個參數，因此利用 `apply` 傳入這些 `argument`來執行`callback` ( 這是一個非常好的 `apply` 使用範例！ )。

發現了嗎，這裡也有用了 [Closure](https://ithelp.ithome.com.tw/articles/10195343) 的威能，這個 `bind` 的polyfill才能預先儲存傳入的 `arguments`。

### 使用情境

由範例可以很清楚的知道， `bind` 除了硬繫結 this 以外，還可以預先帶入參數給目標函式。**當我們必須先記錄住部分參數，又或者對函式不斷放入固定幾個參數做操作的時候**，`bind` 就會是一個良好的使用時機。另外，在某些特定的狀況下，`bind` 也能作為簡化的語法使用。



## 小結

本篇介紹了 `call`、`apply`、`bind` 三個 Function 的內建函式，三者很像，卻都有自己的特異處。

`call`：

* 語法：`fn.call(this, arg1, arg2..., argn)`
* 使用情境：使用情境只在於要明確指定 `this` 的時候

`apply`：

- 語法：`fn.apply(this, [arg1, arg2..., argn])`
- 使用情境：使用情境只在於要明確指定 `this` 的時候

`bind`：

- 語法：`fn.bind(this, arg1, arg2..., argn)`
- 使用情境：使用情境只在於要明確指定 `this` 的時候



## 參考

[You Don't Know JS: Scope & Closure](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)
