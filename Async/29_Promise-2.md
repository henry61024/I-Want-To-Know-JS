# JavaScript Promise (2)

在 [上一篇](https://ithelp.ithome.com.tw/articles/10197427) 中，我們已經介紹了傳統 callback 的缺點，以及 Promise 是如何克服這些缺點的。

而這篇將專注於解釋 Promise 的意義以及用法。



## Promise 的意義

Promise，如字面的意思就代表 **承諾**。

### 承諾的未來

當你拿到一個 Promise 的時候，代表在未來中這個 Promise 可能會有幾種狀況發生

* 承諾 **被兌現 (fulfilled)**

  用 `resolve()` 來兌現

* 承諾 **被打破 (rejected)**

  用 `reject()` 來表示失敗

* 承諾 **一直沒有回應 (pending)**

  一直沒有回傳

也就是說，承諾代表的不見得是成功。

### 未來之後

而根據這三種結果，我們接下來的動作會有所不同，如果：

* 承諾被兌現 就 **繼續做預定好的下一件事**

  使用 `.then()`

* 承諾被打破 就 **根據這個原因去做對應的動作**

  使用 `.catch()`，或是 `.then` 的第二個參數

* 承諾 一直都沒有回應 就 **繼續等下去**



## Promise 用法

### 創建 Promise

首先要先了解到底要怎麼創建一個 Promise。

創建 Promise 很簡單，只要 `new` 一個 `Promise` 物件出來即可：

```javascript
var a = new Promise(function(resolve, reject) {
			// sync or async codes
  			// if success, resolve
  			// if fail, reject
		});
```

`Promise` 可以帶入一個函式，代表著要給予承諾的程式。

這個函式會被 Promise 傳入兩個參數，這兩個參數也是函式，他們分別代表：

* **兌現 (fulfilled)**

  通常以 `resolve` 命名該參數

  當我們完成動作時，就呼叫 `resolve()` 來兌現我們的承諾

* **拒絕 (rejected)**

  通常以 `reject` 命名該參數

  當我們動作失敗時，就呼叫 `reject()` 來打破我們的承諾



### Promise 被兌現 (fulfilled)

接下來讓我們看看要怎麼兌現承諾，以及兌現之後要怎麼接續動作：

```javascript
var a = new Promise(function(resolve, reject) {
			setTimeout(function(){
				resolve('hello world');
			}, 1000);
		});

a.then(function(value) {
  console.log(a);				// Promise {<resolved>: "hello world"}
  console.log(value + '1');		// "hello world1"
});
a.then(function(value) {
  console.log(a);				// Promise {<resolved>: "hello world"}
  console.log(value + '2');		// "hello world2"
});

console.log(a);					// Promise {<pending>}
```

先來看我們非同步的函式 `setTimeout` 這邊。很清楚的可以看到，這個非同步會在 `1000` 毫秒後 兌現承諾 ( 因為呼叫了 `reject()` )。

那等待兌現承諾著這一秒期間發生了什麼事情呢？讓我們往後面的程式碼看。我們緊接著會看到兩句 `a.then`，還記得前面說過的，`.then()` 是代表承諾被實現之後才會去執行的，因此在這一秒內，我們都不會看到 `a.then` 會印出東西。

接著我們在看最後一句：`console.log(a)`，這句並沒有被 `then` 所包住，因此這並不會等待 Promise 完成後才執行，就像 [等餐的時候我們還是可以滑手機](https://ithelp.ithome.com.tw/articles/10197312) 一樣，`console.log(a)` 這句就代表滑手機這個動作，因為它與 Promise 對先與否不相關，所以可以先做。此時印出 `a` 會發現他的值是 **`"Promise {<pending>}"`** ，這就代表**承諾還沒有回應，等待中**。

一秒到了，`a.then` 就會緊接著被執行。可以注意到的是，`a` 的值是可以保留下來的，因此無論你接了幾個 `then`，他都可以偵測到 `a` 這個承諾的完成狀況。因此我們接著就會印出 `Promise {<resolved>: "hello world1"}`、`"hello world1"` 與 `Promise {<resolved>: "hello world2"}`、`"hello world2"` 兩組。可以看到此時 `a` 的值變成了 **`Promise {<resolved>: "hello world"}`**，這代表此**承諾已經被兌現**，而兌現後他會給我們 `"hello world"` 這個值。

### Promise 被拒絕 (rejected)

有了這個概念後，了解如何接收被拒絕的 Promise 就很簡單了

```javascript
var a = new Promise(function(resolve, reject) {
			setTimeout(function(){
				reject('OOPS');
			}, 1000);
		});

a.catch(function(value) {
  console.log(a);				// Promise {<reject>: "OOPS"}
  console.log(value);			// "OOPS"
});
```

我們的非同步程式邏輯不變，只把 `resolve` 改成 `reject`。

之後，我們使用 `.catch` 來抓住錯誤。過了一秒後，我們就可以得到 `Promise` 拒絕承諾的訊息了。

### Promise 一直沒有回應 (pending)

最後的 pending 就最好示範了。

當我們並沒有成功 `resolve` 或是 `reject` 的時候，就會 pending，也就是說 `.then` 內的程式會一直等待，而 `.catch` 也不會抓到任何錯誤。

```javascript
var a = new Promise(function(resolve, reject) {});

console.log(a);					// Promise {<pending>}
```

雖然這些例子都有點刻意，但是卻都很容易出現在日常中。像是當我們試圖去連到一個網站，而他卻一直沒有回應時，我們的 Promise 就會一直處於 pending 狀態。



## Promise Chain

當你可以等待一個非同步動作完成後，再去執行下一個動作時，那接著你可能就會想說，如果有很多非同步動作要串接在一起的話要怎麼辦呢？

很幸運的，`.then` 可以串接非同步程式。講得更精確一點是，`.then` 不論是非同步或者同步的程式都可以串接，而 `then` 會回傳一個 `Promise`。既然他又**會回傳一個 `Promise` 就代表我們可以串接非同步程式**。

也許你會很疑惑的問，假如我們的 `.then` 執行的是一個同步程式的話，那他還會回傳 `Promise` 物件嗎 ( 畢竟非同步才需要承諾 )？答案是會，`.then` 會把我們的 `return` 值包裹成一個 `Promise`，並回傳回去。

讓我們來看看範例：

```javascript
asyncFn()
.then(syncFn)
.then(asyncFn);

function asyncFn(data) {
	return new Promise(function(resolve, reject) {
      		 console.log('Async received data:', data);
			 setTimeout(function(){
				resolve('async fn');
			 }, 1000);
		   });
}

function syncFn(data) {
  console.log('Sync received data:', data);
  return 'sync fn';
}

// "Async received data: undefined"
// "Sync received data: async fn"
// "Async received data: sync fn"
```

在這裡，我們先使用 `asyncFn` 回傳一個 Promise 回去。一秒後，`syncFn` 收到了來自 `asyncFn` 的兌現的承諾值 `"async fn"`，在印出 `"Sync received data: async fn"` 之後，便 `return 'sync fn'`。接著，JavaScript 會把 `'sync fn'` 包裹成一個 `Promise`，因此我們可以再繼續 Chain 到下一個 `.then` 執行 `asyncFn`。

**這就證明了不論是非同步或者同步的程式都可以串接，而 `then` 會回傳一個 `Promise` 好讓我們繼續串接**。



## 小結

從這篇中，我們可以學到 **Promise** 代表的意思就是 **承諾**，承諾的未來有幾種可能：

- 承諾 **被兌現 (fulfilled)**

  用 `resolve()` 來兌現

- 承諾 **被打破 (rejected)**

  用 `reject()` 來表示失敗

- 承諾 **一直沒有回應 (pending)**

  一直沒有回傳

而在這些承諾的未來之後，如果：

- 承諾被兌現 就 **繼續做預定好的下一件事**

  使用 `.then()`

- 承諾被打破 就 **根據這個原因去做對應的動作**

  使用 `.catch()`，或是 `.then` 的第二個參數

- 承諾 一直都沒有回應 就 **繼續等下去**

實際上 Promise 還有一些 API 可以供我們使用，這部分可以參照 [MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise) 去更深入了解。



## 參考

[You Don't Know JS: async & performance](https://github.com/getify/You-Dont-Know-JS/tree/master/async%20%26%20performance)

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise