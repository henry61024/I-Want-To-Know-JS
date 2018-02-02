# 屬性描述器

在 ES5 之後，JavaScript 允許開發者調整**屬性的描述器**。

在此之前，先讓我們重新認識一下屬性。

常常我們會認為屬性就是 key/value 的組合，實際上確實是如此，但除此之外，還有一些 屬性的**設定** 藏在裡面，我們就把這些屬性的**設定** 稱為 **屬性的特徵**，而供我們設定這些屬性特徵的函式我們就把它稱為 **屬性描述器**。



使用時機

優點

缺點

覆蓋屬性特徵



## 屬性的特徵

也就是 ***屬性的設定***

實際上，屬性特徵有以下六種：

- writable
- configurable
- enumerable
- value
- get
- set

而這些特徵都是可以透過屬性描述器去設定的 ( `Object.defineProperty` 與 `Object.definedProperties` )



## 屬性描述器

在 ES5 之後，我們就可以使用 `Object.defineProperty` 與 `Object.definedProperties` 來設定屬性的特徵。

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

換句話說，可以控制屬性是否為 read-only

當我們使用屬性的字面值 ( `obj.prop` 與 `obj[prop]` ) 定義屬性時，屬性的 `writable` 相當於 `true`，也就代表可以寫入。

那就讓我們來看看當 `writable: false` 時，嘗試寫入會發生什麼事情：

### read-only 時無法寫入

在非嚴格模式下，還是可以對 `writable: false` 的屬性做寫值的動作，但是會沒有效果：

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

預設值?



## Configurable

代表 ***是否可改變該屬性描述器/刪除該屬性***

首先我們要有一個概念是，屬性描述器在一般狀況下是可以被重新定義的

```javascript

```



可覆蓋原有的屬性特徵 ( when `configuratable: true` )

```javascript
var obj = {};
Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    configurable: false,
    enumerable: true,
    writable: true
});
console.log(obj.prop1);           // 'This is prop1'

Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    configurable: true,
    enumerable: true,
    writable: false
});                               // Uncaught TypeError: Cannot redefine property: prop1
```



```javascript
delete obj.prop1;                 // false
```



```javascript
'use strict';
delete obj.prop1;                 // Uncaught TypeError: Cannot delete property 'prop1' of #<Object>
```





## Enumerable



```javascript
var obj = {};
Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1',
    configurable: true,
    enumerable: false,
    writable: true
});
console.log(obj.prop1);           // 'This is prop1'

console.log('prop1' in obj);      // true
for(var prop in obj) {
    console.log('prop:', prop)    // undefined
}
```



檢查屬性是否可以列舉

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





## 取值器



## Value



```javascript
var obj = {};
Object.defineProperty(obj, 'prop1', {
    value: 'This is prop1'
});
console.log(obj.prop1);           // 'This is prop1'
```





## Getter



```javascript
var obj = {
    get prop2() {
        return 'This is prop2';
    }
};
Object.defineProperty(obj, 'prop1', {
    get: function() {
        return 'This is prop1';
    },
    configurable: true,
    enumerable: true
});
console.log(obj.prop1);           // 'This is prop1'
console.log(obj.prop2);           // 'This is prop2'

console.log('prop1' in obj);      // true
for(var prop in obj) {
    console.log('prop', prop)     // undefined
}
```







## Setter



```javascript
var obj = {
    set prop2(val) {
        this._prop2_ = val;
    }
};
Object.defineProperty(obj, 'prop1', {
    get: function() {
        return this._prop2_;
    },
    set: function(val) {
        this._prop2_ = val * 2;
    },
    configurable: true,
    enumerable: true
});
obj.prop1 = 1;
obj.prop2 = 1;
console.log(obj.prop1);           // 1
console.log(obj.prop2);           // undefined
```

