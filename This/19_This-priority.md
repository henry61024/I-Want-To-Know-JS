# JavaScript This Priority

在前兩篇中我們詳細的介紹了 this 的五種繫結：

- 預設繫結
- 隱含繫結
- 明確繫結
- new 繫結
- 語彙繫結

那當出現了同時符合兩種繫結的套用規則時，到底誰的優先權比較高呢？

在這篇中，我們會區分出 this 繫結規則優先權的高低，並讓之後看到 this 時，能夠正確的知道它的內容是甚麼。

由於預設繫結是在最陽春的狀況下才會套用，因此就不多著墨比較了。

首先就讓我們先把 隱含繫結 跟 其他繫結 做比較吧！



## 隱含繫結 vs 明確繫結

這個範例程式中，我們首先把 `logMyString` 這個 Function 設給 `obj` 的屬性 `log`，因此執行 `obj.log()` 時就會是套用 隱含繫結，其 this 會是 `obj`。

那假如我們使用 `apply` 來執行 `obj.log` 這個方法的話，究竟是會套用 隱含繫結 還是 明確繫結 呢？

```javascript
var obj = { myString: 'hello obj', log: logMyString },
    obj1 = { myString: 'hello obj1' };
function logMyString() {
	console.log(this.myString);
}
obj.log();						// "hello obj"
obj.log.apply(obj1);			// "hello obj1"
```

執行 `obj.log.apply(obj1);` 後我們可以看到 明確繫結 取勝了，此時吃到的 `this` 會是 `obj1`，它把 隱含繫結 的 `this`：`obj` 給覆蓋掉了。

很明顯的 **明確繫結 > 隱含繫結**。



## 隱含繫結 vs new 繫結

接著來比較 隱含繫結 與 new 繫結。

這裡我們同樣使用 `obj.log` 來試圖把 `logMyString` 的 `this` 設為 隱含繫結，然而這邊我們使用 `new` 把 `obj.log` 當作 建構式 執行：

```javascript
var obj = { myString: 'hello obj', log: logMyString };
function logMyString() {
	this.myString = 'hello constructor';
}
var newObj = new obj.log();
console.log(obj.myString);					// "hello obj"
console.log(newObj.myString);				// "hello constructor"
```

可以發現，new 繫結 蓋過了 隱含繫結，因為我們 `obj.myString` 的值依然保持著 `"hello obj"`，而被 `new` 新建出來的物件則多了一個 `myString` 屬性，裡面的內容確實為我們給的 `"hello contructor"`。

因此我們得到了 **new 繫結 > 隱含繫結** 的結論。



## 明確繫結 vs new 繫結

現在兩個贏家 明確繫結 與 new 繫結要來做個大比拚了。

要先解釋的是，因為我們無法把 `call`、`apply` 拿去跟 `new` 去做比較，因為他們同時都是執行 Function 的方法。因此我們從明確繫結中派出了 `bind` 來跟 `new` 做比較。

按照之前我們所製作的 [bind polyfill](https://ithelp.ithome.com.tw/articles/10196088) 推測，`new` 應該是無法覆蓋 `bind` 指定的 `this` 的，因為 polyfill 中我們是將傳入 `bind` 的第一個參數，利用 `apply` 再指定給目標函式的。就讓我們實際測看看。

```javascript
var obj = { myString: 'hello obj' };
function logMyString() {
	this.myString = 'hello constructor';
}
var bindedFn = logMyString.bind(obj),
    newObj = new bindedFn();
console.log(obj.myString);					// "hello obj"
console.log(newObj.myString);				// "hello constructor"
```

我們將 `logMyString.bind(obj)` 回傳的強制繫結包裹函式指派給了 `bindFn`，並再用 `new` 去執行它。很驚訝地發現，new 繫結 的優先權 竟然大於 `bind`。

我們可以發現 [MDN 上的 bind polyfill](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 較為複雜，而它可以在判斷是 new 繫結 的時候就選擇使用 new 繫結的 this，而非之前強制綁定的 this。

再次的由 new 繫結 取勝。**new 繫結 > 明確繫結**。



## 語彙繫結 vs 明確繫結

這次就由 語彙繫結 來跟 明確繫結 做比較。

```javascript
var obj = { myString: 'hello obj' },
    obj1 = {myString: 'hello obj1'};
function outer() {
    return () => {
    	console.log(this.myString);
    }
}
var arrowFn = outer.apply(obj);
arrowFn();							// "hello obj"
arrowFn.call(obj1);					// "hello obj"
new arrowFn();						// Uncaught TypeError: arrowFn is not a constructor
```

為了能更清楚辨別 語彙繫結的 this，我們先把 `outer` 中的 this 利用 `apply` 指定為 `obj ` (這只是前置步驟而已)。接下來確定一下 `arrowFn` 的 this 真的有綁訂到 `obj`。沒錯，執行完的結果確實是`obj.myString` 的值 `"hello obj"`。

重頭戲來了，接著我們試圖利用 `call(obj1)` 來試圖將 `arrowFn` 內的 this 綁定為 `obj1`。結果發現語彙繫結還是更勝一籌，`arrowFn` 的 `this.myString` 仍然印出了 `obj.myString` 的值，也就是 明確繫結 無法覆蓋由 語彙繫結指定的 this。

這符合我們之前利用 [Closure 來自製 語彙繫結](https://ithelp.ithome.com.tw/articles/10196088) 的行為，因為在自製的語彙繫結中，因為我們只操作變數內容為外層變數的 this，因此我們並不在意 `arrowFn` 的 `this` 為何。

因此 **語彙繫結 > 明確繫結**。

在最後一行 `new arrowFn()` 中我們可以發現 箭號函式是無法作為 建構式使用的，也就是說 箭號函式是無法跟 new 繫結來做比較的。然而還是回到我們自製的 語彙繫結 其實並不在意繫結到內層 Function 的 this 到底為何，因此在這裡我還是會認為 語彙繫結 > new 繫結。



## 小結

這篇中，我們詳細比較了 this 五種繫結之間套用的優先順序，可以歸納出套用的順序如下

1. 語彙繫結
2. new 繫結 
3. 明確繫結
4. 隱含繫結
5. 預設繫結

會把 語彙繫結 擺在 new 繫結 之前是因為我們自製的 語彙繫結 其實並不在意繫結到內層 Function 的 this 到底為何，而是直接使用外層 Function 的 this，因此才做了這樣的順序安排。



## 參考

[You Don't Know JS: this & object prototypes](https://github.com/getify/You-Dont-Know-JS/tree/master/this%20%26%20object%20prototypes)

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/bind







