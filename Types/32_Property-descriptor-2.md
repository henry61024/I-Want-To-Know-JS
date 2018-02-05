## 取值器 與 設值器

上一篇介紹了幾種屬性的特徵：`writable`、`configurable`、`enumerable`、`value`。尚未介紹的兩種特徵分別是 `get` 與 `set`。

`get` 與 `set` 分別是 取值器 與 設值器，我們可以把他想成是函式。當有設定這兩個特徵時，他們會覆蓋 JavaScript 原有的取值與設值行為 `[[GET]]` 與 `[[SET]]`。

我們又稱 `get` 與 `set` 為 ***存取器描述器 ( accessor descriptor )*** 。



## 宣告方式

存取器描述器 有兩種宣告方式：

- **使用物件字面值時直接定義，語法會如下：**

  ```javascript
  var obj = {
      get propName() {
          // ... do something
          return 'some value';
      },
      set propName(val) {
      	// ... do something
      }
  };
  ```

- **利用屬性描述器定義，語法會如下：**

  ```javascript
  Object.defineProperty(obj, 'prop1', {
      // ...
      get: function() {
          // ... do something
          return 'some value';
      },
      set: function(val) {
          // ... do something
      }
  });
  ```

以上兩種宣告方式都是相同的。

首先我們先來介紹 取值器 `get`。



## Getter

取值器中，需要回傳一個值來當作取值的結果：

```javascript
var obj = {
    get prop1() {
        return 'This is prop1';
    }
};
console.log(obj.prop1);           // 'This is prop1'

obj.prop1 = 'Change value!';
console.log(obj.prop1);           // 'This is prop1'
```

從範例中可以看到，不論我們如何改 `prop1` 的值，都會是取得取值器回傳的 `"This is prop1"`。



## Setter

與取值器相當，設值器則是會在拿到值之後，去做指定的動作：

```javascript
var obj = {
    set prop1(val) {
        console.log('prop1 setted: ', val);
    }
};
obj.prop1 = 'This is prop1';		// "prop1 setted:  This is prop1"

console.log(obj.prop1);				// undefined
```

在範例中，我們試圖將 `"This is prop1"` 設入 `prop1` 中，而因為我們的設值器要求 `prop1` 在拿到值之後隨即印出 log，因此我們就會得到 `"prop1 setted:  This is prop1"` 的結果。

接著，我們試圖取出 `obj.prop1` 的值，但因為我們並沒有設定取值器，因此拿到的 `obj.prop1` 會是 `undefiend`。



## 資料描述器 與 存取器描述器 不相容

要注意的是， **資料描述器**  與 **存取器描述器** 是不相容的。

 **資料描述器**  與 **存取器描述器** 是 **屬性描述器的兩大種類**：

- **資料描述器 ( data descriptor )**

  代表一個屬性的是有值的，也就是此屬性有以下兩個特徵：

  - `value`
  - `writable`


- **存取器描述器  ( accessor descriptor )**

  代表一個屬性的值是由 取值器 與 設值器 所決定，也就是此屬性有以下兩個特徵：

  - `get`
  - `set`

那為何 **資料描述器** 與 **存取器描述器** 是不相容的呢？讓我們邏輯思考一下，如果今天我們的屬性已經設定了 `get` 與 `set`，也就代表我們已經定義了屬性  取值 與 設值 的動作了，那再額外設定 屬性的值 (`value`) 與 是否允許設值(`writable`) 的話，不就行為衝突了嗎？

還是讓我們來看看同時設定 **資料描述器**  與 **存取器描述器** 會發生什麼事：

```javascript
var obj = {}
Object.defineProperty(obj, 'prop1', {
    get: function() {
        return 'This is prop1';
    },
    value: 'test',
  	writable: true
});                // Uncaught TypeError: Invalid property descriptor. Cannot both specify accessors and a value or writable attribute, #<Object>
```

可以看到，JavaScript 並不允許我們這樣做，並會發出一個 `Uncaught TypeError: Invalid property descriptor. Cannot both specify accessors and a value or writable attribute, #<Object>` 的錯誤來警告。



##取值器 與 設值器 的應用

到目前為止，我們已經介紹了 取值器 `get` 與 設值器 `set` 了，然而我們還看不出 取值器 與 設值器 的適用時機，很大一部分的原因是因為上面的兩個範例中，我們都並未將 取值器 與 設值器 搭配使用。

### 取值器 與 設值器 的搭配

那我們要如何發揮出他們原本的威力呢？大致上來講，我們需要三個東西：

- **取值器**

  `getter`

- **設值器**

  `setter`

- **容器**

  值真正被存取的地方

讓我們來看看下面的範例：

```javascript
var obj = {};
Object.defineProperty(obj, 'prop1', {
    set: function(val) {
        this._prop1_ = val * 2;
    },
    get: function() {
        return this._prop1_;
    },
    configurable: true,
    enumerable: true
});

obj.prop1 = 100;
console.log(obj.prop1);           // 200
```

範例中，我們宣告了一個變數 `obj`，並加入了一個屬性 `prop1`，並為這個屬性同時加入 `get` 與 `set`。

現在先讓我們來看看 `get` 與 `set` 函式中的共通點：他們都對 `obj._prop1_` 存取。

在 `set` 中，我們將值 乘上 `2` 存進 `obj._prop1_` 中，在 `get` 則負責取出 `obj._prop1_` 的值。

在主邏輯中，我們將 `obj.prop1` 存入 `100`，也就是說，`setter` 會把 `100 * 2 = 200` 存到 `obj._prop1_` 中。在下一行取 `obj.prop1` 的值時，`getter` 就會回傳 `obj._prop1_` 的值回來。

### 自製 Two-way Binding

還有沒有更實用的用法呢？有的，Lets make a little wheel !! 讓我們試著用 取值器 與 設值器 製作小小的 [Two-way binding](https://stackoverflow.com/questions/13504906/what-is-two-way-binding) 吧。

要怎麼做呢？其實很簡單，轉換一下思維，**把 DOM 的 element 的屬性當作容器** 就好。

如果我們要製作一個 Two-way binding 的 input box 的話：

```html
// HTML
<input type="text" id="myInput">
```

HTML 的部分很簡單，只要加入一個 `<input>` element，並賦予它一個 `id` 以便 select 即可。

而 JavaScript 的部分：

```javascript
// JS
var obj = {
  _myInput_ : document.getElementById('myInput'),
  get input() {
      return this._myInput_.value;
  },
  set input(value) {
      this._myInput_.value = value;
  }
}

obj.input = 'hey';

// type something into input

console.log(obj.input);
```

我們宣告一個物件 `obj`，並賦予這個物件一個 `input` 屬性並定義這個屬性的 `get` 與 `set`。除此之外，為了減少 DOM select 的速度，我們將 `myInput` 這個 DOM element 預先存在 `obj._myInput_` 這個屬性中以便操作。

在 `get` 時，直接取 input box 的 `value` 出來作為 `obj.input` 的值；在 `set` 時，則將 `obj.input` 的值直接設進 input box 的 `value` 中。

藉由這個方式，我們就成功實現了一個可以即時更新的小小 Two-way binding 範例。



## 小結

本篇介紹了 ** 取值器 `get` ** 與 ** 設值器 `set` **，可以把他們當作是函式，定義了我們在 取值/設值 時要做的動作。

取值器 與 設值器屬於 **存取器描述器  ( accessor descriptor )**，與 **資料描述器 ( data descriptor )** ( `writable` 與 `value` )是不相容的。

取值器 與 設值器 的應用很廣，兩者配上 **容器** 搭配使用，可以在 取值/設值 時對參數做一些操作再存起來，更進一步甚至可以用在 Two-way binding 上。



## 參考

[You Don't Know JS: Objects](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md)

[飛肯設計: 進階 JS 班](http://www.flycan.com.tw/course/course-javascript-adv.php)