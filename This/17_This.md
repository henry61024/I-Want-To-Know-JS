# JavaScript This

不像是 `C++` & `Java` 的 this，代表著物件本身，JavaScript 的 this 代表著 上下文 (context)

在 You don't know JS 中，提及了 JavaScript 的 This 有五種規則



## 預設繫結

單獨呼叫函式，且沒有任何的情境物件 (Object method)

```javascript
var myString = 'hello global';
function logMyString() {
  var myString = 'hello local';
  console.log(this.myString);
}
logMyString.myString = 'hello function';

logMyString();						// "hello global"
```

請注意 `hello local` 其實是沒用的，因為我們要找的是 `this.myString` 我們可以知道 `.myString` 會是一個 property，因此在怎樣都不可能找到 `hello local` 這個。因此以下我們就把它拿掉

```javascript
'use strict';
var myString = 'hello global';
function logMyString() {
  console.log(this);				// undefined
  console.log(this.myString);		// Uncaught TypeError: Cannot read property 'myString' of undefined
}
logMyString.myString = 'hello function';

logMyString();
```



```javascript
var myString = 'hello global',
    obj = { logMyString: logMyString, myString: 'hello object' };
function logMyString() {
  console.log(this.myString);
}
logMyString.myString = 'hello function';

logMyString();						// "hello global"
obj.logMyString();					// "hello object"
var logMyString2 = obj.logMyString;
logMyString2();						// "hello global"
```



### 保持使用 use strict



## 隱含繫結



## 明確繫結

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
logMyString.call(null);					// "hello global"
```



```javascript
var myString = 'hello global',
	obj = { myString: 'hello object' };
function logMyString() {
  this.myString = 'hello local';
}
logMyString.myString = 'hello function';

logMyString.call(null);					// "hello Object"
console.log(window.myString);			// "hello local"
```



```javascript
var myString = 'hello global',
	obj1 = { myString: 'hello object1' },
    obj2 = { myString: 'hello object2' };
function logMyString() {
  console.log(this.myString);
}
logMyString.myString = 'hello function';

var bindedFn = logMyString.bind(obj1);			// "hello object1"
bindedFn.apply(obj1);							// "hello object1"
bindedFn.apply(obj2);							// "hello object1"
```









## new 繫結

```javascript
var myString = 'hello global';
function logMyString() {
  console.log(this.myString);
  this.myString = 'hello new object';
  console.log(this.myString);
}
logMyString.myString = 'hello function';
var newObj = new logMyString();						// "hello function"
console.log(newObj.myString);
```





## 語彙繫結

```javascript
var myString = 'hello global',
    obj = { myString: 'hello object' };
function outer() {
  this.myString = 'hello outer';
  function inner(callback) {
  	callback();
  };

  inner(() => { this.myString = 'hello inner' });		// "hello outer"
}

//outer();
outer.apply(obj);		// should fix
var newObj = new outer();						// "hello function"
console.log(newObj.myString);
```



With self









