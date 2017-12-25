## 物件 與 屬性

物件是 JavaScript 中的基礎，這麼說一點也不為過，因為就連一切的根本：根物件 也是一個物件  (註 1 )阿！



## 物件

我們可以把物件想成是 *屬性的容器*。

### 字面值

再 `{}` 放入 屬性，屬性間用逗號串接起來，就是物件的字面值，如果遇到有符號字串，則需要用 `''` ( 或者`"" ` ) 將 key 包起來。

```javascript
var myObj = { prop1: 'hello world', "prop-2": 123 };
```

### 屬性

*屬性就是 key/value 的組合，也是一個容器，內部儲存記憶體位置的指向*

* **key：** 也就是 這個屬性的名稱，任何字串都可以作為 key，JavaScript 中的 Array ( 註 2 )就是一個很好的說明範例。
* **value：** 在 value 中可以放入任何型別的值，當然也包括物件。

以上面的例子來說，`myObj` 的第一個屬性的 key 是 `prop1`，value 為 `'hello world'`。

### 操作 (讀取/新增/更新)屬性

操作屬性主要有兩種方法

* **利用 `.` 操作屬性**

  只能在 key 不包含符號的狀況下使用，因為 JavaScript 並不會知道後面的符號是 key 的一部分

  ```javascript
  var myObj = { prop1: 'hello world', "prop-2": 123 };
  // read property
  console.log(myObj.prop1);			// "hello world"
  console.log(myObj.prop-2);			// NaN
  ```

* **利用 `[]` 操作屬性**

  可接受 key 中有符號

  可以接受變數，會將變數中的值自動轉換成 string 再去讀取

  ```javascript
  var myObj = { prop1: 'hello world', "prop-2": 123, "777": 456 },
      myProperty = 777;
  console.log(myObj[prop1]);				// "hello world"
  console.log(myObj["prop-2"]);			// 123
  console.log(myObj[myProperty]);			// 456

  ```

* **新增/更新 屬性**

  **新增與更新屬性的語法是一樣的**，只要對 屬性 進行指派值的動作，就可以 新增/更新 屬性。

  ```javascript
  var myObj = { prop1: 'hello world' },
      myProperty = 777;
  myObj[prop1] = 'hey world';
  myObj["prop-2"] = 123;
  myObj[myProperty] = 456;

  console.log(myObj[prop1]);				// "hey world"
  console.log(myObj["prop-2"]);			// 123
  console.log(myObj[myProperty]);			// 456
  ```


### 刪除屬性

我們可以利用 `delete` 來刪除屬性

```javascript
var myObj = { prop1: 'hello world', "prop-2": 123, "777": 456 },
    myProperty = 777;
delete myObj.prop1;
delete myObj[myProperty];

console.log(myObj);			// { "prop-2": 123 }
```

### 實作建議

* 盡量使用 `.`，因為易讀性較高，且可以被 自動補完工具( auto complete ) 支援
* 必要時，利用 `[]` 操作屬性
* 新增某個物件的屬性，最好統一在同一個地方，否則會很難辨認這個屬性是從哪裡新增的





## 附註

### 1. 根物件也是 object

在瀏覽器中的根物件是 `window` ，根物件有許多內建函式，而需要記得的是，所有全域變數都會是根物件的屬性。在這裡我們可利用 `typeof` 來看看 `window` 的屬性：

```javascript
console.log(typeof window) = "object";
```

沒錯，所以 JavaScript 中，一切的根本：根物件，就是一個物件。

### 2. Array 是個陣列性格的物件

JavaScript 中的 Array 同樣也可以把他想成是一個屬性的容器。並沒有如其他語言的 Array 一樣是連續的記憶體空間能夠快速存取，套句 JavaScript: The good parts 的方式講

> JavaScript 的 Array 是 陣列性格的物件

換句話說，就是屬性名稱為連續數字的物件而已。

那要如何證明呢? 剛剛有提到過，屬性有幾種特性

* 屬性名稱是字串
* 屬性是可以被刪除的

那就讓我們對 Array 開刀看看吧

```javascript
var myArray = [1, 2, 3];
console.log(myArray);				// [1, 2, 3]

delete myArray["0"];
console.log(myArray);				// [empty , 2, 3]
console.log(myArray[0]);			// undefined
```

由這個範例我們可以看到，我們可以刪除 Array 中，名為 `"0"` 的屬性，也與剛剛所說的 屬性特性 相符。

當然，JavaScript 還是有為這個 "陣列性格的物件" 提供了根多 [內建函式](https://www.w3schools.com/jsref/jsref_obj_array.asp) 以方便操作。



## 參考

[JavaScript : The good parts](http://shop.oreilly.com/product/9780596517748.do)