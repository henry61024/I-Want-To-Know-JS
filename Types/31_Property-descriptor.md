# 屬性描述器

在 ES5 之後，JavaScript 允許開發者調整**屬性的描述器**。

在此之前，先讓我們重新認識一下屬性。

常常我們會認為屬性就是 key/value 的組合，實際上確實是如此，但除此之外，還有一些 屬性的**設定** 藏在裡面，我們就把這些屬性的**設定** 稱為 **屬性的特徵**，而設定這些屬性特徵的函式我們就把它稱為 **屬性描述器**。

### 用處

當對於屬性除了指定 key/value 以外有更進一步的要求時，例如設定屬性為 read-only 甚至是 constant 時，就可以使用 **屬性描述器**。

換句話說，就算沒有屬性描述器，我們依然可以撰寫 JavaScript，但使用 屬性描述器 可以讓我們的 ***程式更為強健***。



## 屬性的特徵

也就是 ***屬性的設定***

###  特徵的種類

實際上，屬性特徵有以下六種：

- writable
- configurable
- enumerable
- value
- get
- set

而這些特徵都是可以透過屬性描述器去設定的 ( `Object.defineProperty` 與 `Object.definedProperties` )

### 使用字面值宣告屬性的特徵

一般在使用屬性字面值宣告屬性的時候：

- `writable`、`configurable`、`enumerable` 都會為 `true`
- `value` 就代表屬性的值
- `get`、`set` 則是沒有設定。



## 取得屬性特徵

如果我們想要了解一個屬性的特徵時，可以使用 `Object.getOwnPropertyDescriptor(object, 'propertyName')` 這個內建函式。

就讓我們用 `Object.getOwnPropertyDescriptor` 來看看字面值宣告的 屬性特徵 是否如我們剛剛所說：

```javascript
var obj = { prop1: 'prop1', prop2: 'prop2' };
Object.getOwnPropertyDescriptor(obj, 'prop1', 'prop2');
// { 
//    value: "prop1",
//    writable: true,
//    enumerable: true,
//    configurable: true
// }
```

與我們前面介紹的一樣，使用字面值創建的屬性，其 `writable`、`enumerable`、`configurable` 都會為 `true`，而 `value` 就會是 此屬性的值 `"prop1"`。

對於一次查看多個屬性的特徵，JavaScript 也提供了內建的函式 `Object.getOwnPropertyDescriptors(object, 'propertyName1', 'propertyName2', ...)` 供開發者使用：

```javascript
var obj = { prop1: 'prop1', prop2: 'prop2' };
Object.getOwnPropertyDescriptors(obj, 'prop1', 'prop2');
// {
//		prop1: { value: "prop1", writable: true, enumerable: true, configurable: true },
//		prop2: { value: "prop2", writable: true, enumerable: true, configurable: true }
// }
```



## 屬性描述器

在 ES5 之前，我們並沒有方式去設定屬性的特徵 (除了 `value` 以外) 。

在 ES5 之後，JavaScript 提供了 `Object.defineProperty` 與 `Object.definedProperties` 這兩個屬性描述器介面，讓開發者在創建屬性的同時也設定屬性的特徵。

### Object.defineProperty

當我們需要設定單一個屬性的描述器時，可以使用 ` Object.defineProperty`。

讓我們先來看看他的語法：

```javascript
Object.defineProperty(object, 'propertyName', descriptor);
```

其中，`descriptor` 是一個 Object，`descriptor` 裡面的屬性可以是剛剛提到屬性特徵。

讓我們來看看範例：

```javascript
var obj = {};
Object.defineProperty(obj, 'prop', {
	writable: false,
	configurable: true,
	enumerable: true,
	value: 'This is prop'
});
console.log(obj.prop);		// "This is prop"
```

這樣，我們就可以在 `obj` 中按照我們的需求設定好 `prop` 這個屬性了。

### Object.defineProperties

當我們需要一口氣設定多個屬性的時候，`Object.defineProperties` 就會是一個很好用的工具。

其語法如下：

```javascript
Object.definedProperties(object, properties);
```

其中 `properties` 也是一個 Object，其結構會是這樣：

```javascript
// Properties Object
{
  	'properyName1': descriptor1,
  	'properyName2': descriptor1,
  	// ...
  	'properyNamen': descriptorn
}
```

讓我們來看看範例：

```javascript
var obj = {};
Object.defineProperties(obj, {
  	'prop1': {
        writable: false,
        configurable: true,
        enumerable: true,
        value: 'This is prop1'
  	},
  	'prop2': {
        writable: false,
        configurable: true,
        enumerable: true,
        value: 'This is prop2'
  	}
});
console.log(obj.prop1);			// "This is prop1"
console.log(obj.prop2);			// "This is prop2"
```

在大致上了解了屬性描述器的語法之後，就讓我們開始深入了解 各個屬性的特徵。



## Writable

代表 ***屬性是否可改值***

### 設定屬性為 read-only

換句話說，`writable` 可以控制屬性是否為 read-only

當我們使用屬性的字面值 ( `obj.prop` 與 `obj[prop]` ) 定義屬性時，屬性的 `writable` 為 `true`，也就代表可以寫入。相較之下，當 `writable: false` 就代表此屬性為 `read-only`。

就讓我們試試在 read-only 時嘗試寫入值會發生什麼事情：

在 非嚴格模式下，還是可以對 `writable: false` 的屬性做寫值的動作，但是會沒有效果：

```javascript
var obj = {};
Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    configurable: true,
    enumerable: true,
    writable: false
});
console.log(obj.prop1);           // 'This is prop1'
obj.prop1 = 'This is prop2';
console.log(obj.prop1);           // 'This is prop1'
```

從這個範例我們可以看到，在非嚴格模式下，當我們設定了 `prop1` 這個屬性為 `writable: false` 之後再嘗試寫入 `This is prop2` 時，Engine是沒有發出任何的錯誤的，然而當我們再次查看 `obj.prop1` 的值，會發現他還是原本的 `prop1`，也就是我們的寫入動作被無聲無息地忽略了。

相較之下，在嚴格模式下，對 `writable: false` 屬性嘗試寫值就會直接噴 `Uncaught TypeError: Cannot assign to read only property` 這個錯誤。

```javascript
'use strict';
obj.prop1 = 'This is prop2';
console.log(obj.prop1);           // Uncaught TypeError: Cannot assign to read only property 'prop1' of object
```

另外， `writable` 也是 ***資料描述器 ( data descriptor )*** 的一環。



## Configurable

代表 ***是否可改變該屬性的特徵/刪除該屬性***

### 屬性特徵可以被重新設定
還有一點我們需要知道，屬性描述器在一般狀況下是可以利用屬性描述器重新設定的，沒有重新設定到的特徵會保留原有的特徵。

讓我們考慮以下程式：

```javascript
var obj = {};
obj.prop1 = 'This is prop1';

Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    configurable: true,
    enumerable: true,
    writable: false
});
console.log(obj.prop1);           // "This is prop1"
obj.prop1 = 'This is prop2';
console.log(obj.prop1);				// "This is prop1"
```

從範例中我們可以看到，`obj.prop1` 的 `writable: true` 被覆蓋為 `writable: false` 了，而沒有設定到的 `value` 則還是保持 `"This is prop1"`。此時試圖將 `obj.prop1` 重新賦值為 `"This is prop2"` 失敗，因此設定 `writable: false` 這個動作是成功的。

### 禁止屬性被重新設定

當我們將 `configurable: false` 時，我們就可以禁止屬性被重新設定。

考慮以下程式：

```javascript
var obj = {};
Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    configurable: false,
    enumerable: true,
    writable: true
});
console.log(obj.prop1);           // "This is prop1"

Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    configurable: true,
    enumerable: true,
    writable: false
});                               // Uncaught TypeError: Cannot redefine property: prop1
```

當我們在 `obj.prop1` 已經被設定為 `configurable: false` 的狀況下，又試圖重新設定屬性描述器一次時，JavaScript 就直接跳出 `Uncaught TypeError: Cannot redefine property: prop1` 的錯誤。可見即便在非嚴格模式下，JavaScript 都不允許我們重新設定 `configurable: false` 的屬性描述器。

**但有個特例：**在 `configurable: false` 的狀況下， **`writable` 特徵還是可以從 `true` 被改成 `false` 的** 。

### 禁止屬性被刪除

另外，當 `configurable: false` 的時候，屬性也是禁止被刪除的。

承接上面的程式：

```javascript
delete obj.prop1;                 // false
console.log(obj.prop1);				 // "This is prop1"
```

在 非嚴格模式下，當我們使用 `delete` 刪除 `configurable: false` 的屬性時，會回傳 `false` 來表示這個動作失敗。

那如果在 嚴格模式下 呢？

```javascript
'use strict';
delete obj.prop1;                 // Uncaught TypeError: Cannot delete property 'prop1' of #<Object>
```

JavaScript 直接跳出了 `Uncaught TypeError: Cannot delete property 'prop1' of #<Object>` 的錯誤訊息告訴我們 刪除 `configurable: false` 的函式是不合法的。




## Enumerable

代表 ***屬性是否可列舉***

換句話說就是，屬性是否會在物件的屬性列舉時被顯示。

例如在 `for..in` 的屬性列舉動作中，只有可列舉的屬性會被迭代。

### 禁止屬性被列舉

當我們將屬性的 `enumerable` 設為 `false`，就可以防止屬性被列舉：

```javascript
var obj = {};
Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    configurable: true,
    enumerable: false,
    writable: true
});
obj.prop2 = 'This is prop2';

console.log('prop1' in obj);      // true
console.log('prop2' in obj);      // true
for(var prop in obj) {
    console.log('prop: ', prop)    // "prop: This is prop2"
}
```

可以看到，雖然 `prop1` 與 `prop2` 屬性都存在 ( 利用 `in` 檢查 )，但因為 `obj.prop1` 被設為 `enumerable: false` 因此在 `for..in` 列舉屬性動作中，並不會被迭代到。相較之下，普通的屬性 `obj.prop2` 則依然是可被列舉的。


### 檢查屬性的可列舉性

利用 `obj.propertyIsEnumerable`，我們可以檢查一個屬性 ***是否可列舉*** 且為 ***物件自有*** 的：

```javascript
var obj = { prop1: 'prop1' };
Object.defineProperty(obj, 'prop2', {
    value: 'prop2',
    enumerable: false,
    writable: true,
    configurable: true
});
obj.propertyIsEnumerable('prop1');        // true
obj.propertyIsEnumerable('prop2');        // false
```

可以看到，`obj.prop1` 顯示是可列舉的，而被設為 `enumerable: false` 的 `obj.prop2` 則是不可列舉的，符合我們的預期。

除此之外，使用 `Object.keys` 會將所有可列舉的屬性列成一個陣列：


```javascript
var obj = { prop1: 'prop1' };
Object.defineProperty(obj, 'prop2', {
    value: 'prop2',
    enumerable: false,
    writable: true,
    configurable: true
});
Object.keys(obj);            // ["prop1"]
```

因為 `obj` 中只有 `prop1` 可列舉，`Object.keys` 中列出的陣列只有 `prop1` 這個 key。




## Value

代表 ***屬性的值***

`value` 大概是屬性特徵中最常見的一個了，他代表著屬性的值：

```javascript
var obj = {};
Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    writable: true,
    configurable: true,
    enumerable: true
});
console.log(obj.prop1);           // "This is prop1"
```

而以上這段範例，其實就相當於：

```javascript
var obj = {};
obj.prop1 = 'This is prop1';
console.log(obj.prop1);				// "This is prop1"
```

另外， `value` 也是 ***資料描述器 ( data descriptor )*** 的一環。



## 屬性描述器屬於淺層設定

前面介紹了 屬性描述器 與 各種屬性特徵，但我們需要知道這些屬性描述器只會 ***淺層設定屬性特徵*** 而已。

何謂淺層設定呢？就是只有目標物件的 ***自有屬性 才會擁有這個特徵***，若屬性又指向了另一個物件，則另一物件內的屬性則不算 自有屬性，也就不會擁有這個特徵。

讓我們以 `writable: false` 來舉例：

```javascript
var obj = {},
    innerObj = { innerProp: 'This is innerProp' };
Object.defineProperty(obj, 'prop1', {
  value: innerObj,
  writable: false,
  configuration: true,
  enumerable: true
});

obj.prop1 = {};
console.log(obj.prop1);		// { innerProp: "This is innerProp" }

obj.prop1.innerProp = 'innerProp changed!';
console.log(obj.prop1);		// { innerProp: "innerProp changed!" }
```

在範例中，我們將 `obj.prop1` 設成 `writable: false` 並賦值為 `innerObj`，接著試圖將 `{}` 寫入 `obj.prop1`。如我們預期的，這個寫入動作沒有成功，`obj.prop1` 的值依然指向 `innerObj`。

那如果我們複寫 `innerObj` 的屬性 `innerProp` 的話，寫入這個動作會被允許嗎？答案是肯定的，因為只有 `obj` 自身的屬性 `prop1` 被指定為 `writable: false`，而 `prop1` 指向的 `innerObj` 內部屬性 `innerProp` 則不受 `prop1` 的特徵管轄，因此複寫 `innerProp` 是可以的。

我們需要熟知這個特性，以免誤會設定屬性特徵這個動作也會深層影響到 非自有的內層屬性。



## 小結

本篇介紹了 **屬性描述器**，*屬性描述器就是設定屬性特徵的函式*。

在 ES5 之後，JavaScript 提供了  `Object.defineProperty` 與 `Object.definedProperties` 作為屬性描述器提供開發者使用。

若我們要了解一個屬性的特徵，則可以使用 `Object.getOwnPropertyDescriptor` 與 `Object.getOwnPropertyDescriptors`。

而 **屬性的特徵** 就代表著屬性的一些細節設定，總共有六種屬性特徵：

* **writable**

  屬性是否可改值

* **configurable**

  是否可改變該屬性的特徵/刪除該屬性

* **enumerable**

  屬性是否可列舉

* **value**

  屬性的值

* get

* set

屬性描述器可以有效的增加程式的強健性，但需要注意一點的是，屬性描述器只會 ***淺層的設定屬性特徵*** 。



## 參考

[You Don't Know JS: Objects](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md)

[飛肯設計: 進階 JS 班](http://www.flycan.com.tw/course/course-javascript-adv.php)