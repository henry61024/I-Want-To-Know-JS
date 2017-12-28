# JavaScript Function

*我們可以把 JavaScript Function 當作是 可執行的物件。*

JavaScript Function 有兩大特性：

- 是 First-class Object
- 有自己的 Scope

其中，Scope 的部分將會留到下一篇介紹。



## First-class Object

Function 是 JavaScript 的 First-class object，那何謂 First-class object 呢? 簡單來講就是

***對物件型別能做的事情，也能對 Function 做***

也就是我們能夠：

- 把 Function 自由指派給 變數/屬性
- 為 Function 加上屬性
- 當作 Expression 使用
- 可以在任何地方宣告
- ...etc

就讓我們來一一驗證。

### 把 Function 自由指派給 變數/屬性

```javascript
function myFunction() {
  console.log('This is myFunction');
}
var a = myFunction,
    myObj = { fn: myFunction };
console.log(a);							// f myFunction() { console.log('This is  myFunction'); }
console.log(myObj.fn);					// f myFunction() { console.log('This is  myFunction'); }
a();									// "This is myFunction"
myObj.fn();								// "This is myFunction"
```

從範例中能看到，function 可以被自由的指派給其他變數的，當我們印出 `a`/`myObj.fn` 的時候，就會 log 出整個 `myFunction` 的內容。此時 變數/屬性 的內容是 myFunction，因此當我們加上 `()` 時，就會執行 `myFunction`。

### 為 Function 加上屬性

```javascript
function myFunction() {
  console.log('This is myFunction');
}
myFunction.prop1 = 'This is prop1';
console.log(myFunction.prop1);					// "This is prop1"
myFunction();									// "This is myFunction"
```

如同 物件一樣，我們可以自由的在 Function 上加上屬性，並且就算加完了屬性，Function 還是可以正常運作。

### 當作 Expression 使用

也就是說，Function 可以搭配各種運算元使用，甚至也可以放在 `if` 中判斷他是 truthy 還是 falsy。

在這邊，我們用之前提到的 NOT 運算元 `!` 來試試看能不能將 function 放入 Expression 中：

```javascript
function myFunction() {
  console.log('This is myFunction');
}
console.log(!myFunction);					// false
```

結果讓我們確定 function 也可以加入 Expression，因為 function 不在 `falsy` 之列，經由 NOT 運算元後印出的就會是 `false`。

### 可以在任何地方宣告

如物件一樣，我們能夠在任何地方宣告 function，例如，在 function 中還能在宣告一個 function，甚至也能在傳入參數時宣告。不論是何種宣告方式，這些 function 都是可以正常執行的：

```javascript
function myFunction(callback) {
  console.log('This is myFunction');			// This is myFunction
  callback();									// This is myCallback
  function myInnerFunction() {
    console.log('This is myInnerFunction');
  }
  myInnerFunction();							// This is myInnerFunction
}

myFunction(function myCallback() {
  console.log('This is myCallback');
});
```

由上面四個範例中，我們都可以看到 Function 會有物件所有的特性，物件與 Function 的差別只在於 Function 是可以被執行的，因此說我們才說 **Function 是一個可執行物件**。



## 宣告 Function

宣告 function的方式主要三種：

- Function declaration
- Function Expression
- With Function Constructor

### Function declaration

Function delclaration 是最基本宣告 function 的方式，與其他語言宣告 function 的方法相同：

```javascript
function myFunction() {
  console.log('Function declared with Function declaration');
}
```

### Function Expression

如上面所提，Function 可以被傳遞給其他參數儲存。如字面值一般，Function 也可以在定義的同時賦予變數，這種方法就稱為 Function Expression，如下所示：

```javascript
var myFunction = function NamedFunction() {
  console.log('Function declared with Function expression');
}
myFunction();				// "Function declared with Function expression"
```

另外，因為利用 Function Expression 會將 Function 傳遞給一個變數，因此 JavaScript 允許 Function 搭配 **匿名函式**(不具名字的函式) 使用：

```javascript
var myFunction = function() {
  console.log('Function declared with Function expression');
}
myFunction();				// "Function declared with Function expression"
```

透過以上兩個範例，可以看到不論是以 具名函式 或是以 匿名函式，執行出來的結果都是相同的。

### With Function Constructor

與其他的 內建物件型別 相同，Function 也可以利用 `new` 來新增物件：

```javascript
var myFunction = new Function("parameter1", "parameter2", "console.log('Function declared with Function Constructor')");
myFunction();				// Function declared with Function Constructor
```

 事實上，以上的程式碼可以用 Function Expression 取代

```javascript
var myFunction = function(parameter1, parameter2) {
  console.log('Function declared with Function expression')
}
myFunction();				// "Function declared with Function expression"
```

因此同樣的，少用`new`，以 Function Expression 取代 `new` 來增加易讀性與效能。



## 呼叫 Function

function 的呼叫方式有三種：

- 一般呼叫
- 使用 Function 內建函式呼叫
- 使用 `new` 呼叫

### 一般呼叫

在 Function 後面加上 `()` 就可以呼叫 Function，例如：

```javascript
myFunction();
```

### 使用 Function 內建函式呼叫

Function 中有兩種內建的函式可以用來呼叫 Function

- `call`
- `apply`

那為何不使用 `()` 來執行 Function 就好呢? 因為他們有 **明確指定 this** 的功能。

`this` 與 `call`、`apply` 的詳細運作原理也會在之後的章節中討論。首先我們還是先上範例：

```javascript
var myFunction = function(parameter1, parameter2) {
  console.log(parameter1, parameter2);
}
myFunction.call(null, 'hello world', 123);		// "hello world" 123
myFunction.apply(null, ['hello world', 123]);	// "hello world" 123
```

如結果所示，`call` 與 `apply` 執行 Function 的功能與 `()` 無異。

`call` 與 `apply` 的差別在於 `apply` 的第二個參數是一個陣列，裡面帶有要傳入 function 中的參數。因此在範例中，我們可以看到 `apply` 的第二個參數為 `['hello world', 123]`，這兩個參數會被攤開為 `parameter1` 與 `parameter2`後放入 `myFunction` 執行。

### 使用 `new` 呼叫

使用 `new` 代表將 Function 當作建構式來使用，會回傳子型別為該 Function 的物件。`new` 詳細的介紹也會在往後篇幅中涵蓋。

使用 `new` 與宣告內建物件型別的方法是一樣的：

```javascript
function myFunction() {
  console.log('Function declared with Function expression');
}
var a = new myFunction();				// "Function declared with Function expression"
console.log(a);							// myFunction {}
```



## IIFE (Immediately Invoked Function Expression)

或稱：**自我調用函式 ( Self-Invoking Functions )**

顧名思義，IIFE 有兩個特性：

- 當程式跑到 IIFE 的宣告位置時，IIFE 就會自動執行。
- 也是一種 Function Expression

### 格式

```javascript
(function() {
  // codes here
})();
```

### 範例

讓我們來看看範例：

```javascript
(function() {
  console.log('This is IIFE');			// "This is IIFE"
})();
```

哇 IIFE 真的不需要再額外呼叫就會自己就執行了。其實重點除了在於包裹 function 的 `()` 以外，還有用來呼叫 function 的 `()`。

### 測試不加上 `() ` 呼叫

讓我們試試不加呼叫 Function 的 `()` 會怎樣：

```javascript
(function() {
  console.log('This is IIFE');
});
```

什麼都沒發生。因此在使用 IIFE 的時候後面務必加上 `()`。

### IIFE 依然可以回傳值給變數接收

除此之外，IIFE 依然可以回傳值，如下：

```javascript
var myString = (function() {
  return 'This is IIFE';
})();
console.log(myString);				// This is IIFE
```

`myString` 可以接收到 IIFE 自動執行完後回傳的字串。IIFE  除了會自動執行以外，與一般 function 的特性都是一模一樣的。



## 名詞釐清

- Function 的稱呼方式：
  - 在一般狀況下就稱作 **函式 ( Function )**
  - 如果 Function 是某物件的屬性，則我們稱此 Function 為該物件的 **方法 ( Method )** 
  - 如果 Function 搭配 `new` 使用，則稱為 **建構式 ( constructor )**




## 小結

本篇中，我們介紹了 Function 相關內容，包括

**JavaScript Function 有兩大特性：**

- 是 First-class Object
- 有自己的 Scope

**First-class Object**：

- 代表對物件型別能做的事情，也能對 Function 做

**宣告 Function 的方式：**

- Function declaration
- Function Expression
  - IIFE
- With Function Constructor

**呼叫 Function 的方式：**

- 一般呼叫
- 使用 Function 內建函式呼叫
- 使用 `new` 呼叫




## 參考

<https://www.w3schools.com/js/js_function_definition.asp>

<https://www.w3schools.com/js/js_function_invocation.asp>

[保哥 JavaScript 開發實戰：核心概念篇](https://www.accupass.com/event/1710170207181263097416)

[飛肯設計: 進階 JS 班](http://www.flycan.com.tw/course/course-javascript-adv.php)
