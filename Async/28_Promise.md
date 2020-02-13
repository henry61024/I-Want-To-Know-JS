# JavaScript Promise (1) 

在上一篇中，我們介紹了 非同步 以及如何用 callback 實作，而這篇我們將開始介紹 ES6 的新語法 Promise。

在介紹 Promise 之前，我們會先更深入的了解一下使用 callback 會遇到甚麼問題。

就讓我們稍微修改 *You Don't Know  JS* 上的例子來做說明吧！

##  callback 的問題

這是個關於信用卡扣款的例子。

今天有一個電商網站，為了想要製作一個慾望清單來增加客戶的購買率。為了製作這個慾望清單，在 survey 之後，決定套用一個專門用來統計資料的函式庫。

這個函式庫需要我們去呼叫一個 叫做 `analyze` 的 API 來做資料的統計。`analyze` 是一個非同步的函式，總共要帶入兩個參數：要統計的資料 `data` 以及 非同步執行完之後的操作 `callback`。

因此套用第三方函式庫的 API 後，程式會變成這樣：

```javascript
analyze({}, chargeMoney.bind(null, 100));

function chargeMoney(money) {
  console.log('charged ' + money);
}

// charged 100
// charged 100
```

一切看起來正常，結果新的版本上線之後，發現有的使用者竟然回報被扣款了兩次！？

事情大條了，但是你百思不得其解，畢竟在你程式中使用到扣款的地方只有單單這一行，而且你還曾為了寫出這段簡潔的程式而沾沾自喜過：

```javascript
analyze({}, chargeMoney.bind(null, 100));
```

你很直覺的就發現可能是這個第三方函式庫有問題，追了一下發現果然：

```javascript
function analyze(data, callback) {
  // analyze data
  callback();
  // some more codes
  callback();
}
```

這個第三方函式庫的 `analyze` API 竟然有 Bug，他呼叫了你的 `callback` 兩次，而你帶入的 `callback` 就偏偏是事關重要的扣款功能。

### 發現 callback 的問題

從這個例子中可以發現，**傳統的 callback 有一個特性：**

* **控制的反轉**

使用傳統的 callback 方法就代表把非同步的控制權交給被呼叫的函式了，你**無法控制被呼叫函式會怎麼呼叫你的 callback**

這就是 **callback 的一個大問題：**

* **可靠性不足**

你無法控制第三方的函式會不會在 ***正確的時間點呼叫*** 以及 ***呼叫正確的次數*** 的 callback 函式，而這可能會引發災難。



## Promise

因此 ES6 新增了一個新語法：`Promise`。

Promise 顧名思義就是一個承諾，如果承諾兌現，就繼續做計畫好的下一件事；如果承諾被打破了，就不繼續做下一件事。

### Promise 語法概述

Promise 的精隨就是要用來解決可靠性問題。先來看看 Promise 的用法：

```javascript
var defer = new Promise(function(resolve, reject) { 
        // if success, resolve()
        // if fail, reject()
	})
	.then(laterOn)
	.catch(printError);
```

我們可以使用 `new Promise` 來創建一個新的 Promise。Promise 可以帶入一個函式，而這個函式又會被傳入兩個參數：`resolve` & `reject`。

**如果我們的 Promise 成功完成**的話，就可以呼叫 `resolve()` 來回傳 Promise，接著我們就可以用 `then` 來接續這個非同步執行完後要做的動作。

**如果我們的 Promise 失敗**的話，就可以呼叫 `reject()` 來回傳 失敗原因，接著我們就可以用 `catch` 來接住錯誤。

當 Promise 被解析成 resolve 或 reject 之後，其值就無法再被改變。

### 回到範例

接著讓我們回到信用卡的範例，但這次，我們要試試看 Promise 能不能真的解決可靠性的問題。

先來看我們的第三方函式庫，假設他們是使用 Promise 的語法犯了錯，很不小心的執行了 resolve 兩次：

```javascript
function analyze(data) {
  return new Promise(function(resolve, reject) {
	// something else
    // analyze data
    resolve();
    // some more codes
    resolve();
  })
}
```

而我們的程式使用 Promise 改成這樣：

```javascript
analyze({})
  .then(chargeMoney.bind(null, 100));

function chargeMoney(money) {
  console.log('charged ' + money);
}

// charged 100
```

很神奇的，竟然換成 Promise 之後就沒有被重複扣款了！？

這是因為我們前面所說的：**當 Promise 被解析成 resolve 或 reject 之後，其值就無法再被改變**，而就算你呼叫 resolve 兩次，第二個 resolve 也不會再被解析。

更重要的是，我們現在不在需要傳入 callback 給 `analyze` ，而是換成 `analyze` 回傳的是一個 Promise，等到 `analyze` 完成了自己的非同步動作，才會由我們執行 `chargeMoney` 函式，而我們也並沒有 `chargeMoney` 兩次，所以他當然只會扣一次款而已。

### 發現 Promise 的精隨

因此我們可以看到 **Promise 的兩大重點**：

- **可靠**

  我們可以信任 Promise 回傳的東西，這回傳值一旦回傳就無法被更改。

* **控制回歸**

  我們將執行 callback 的控制權拿回來了，現在如何使用 callback 由我們自己操控。



## 小結

在這篇中，我們探討了 **callback 的特性 與 問題**：

* **callback 特性：控制的反轉**

  因此造成了以下的問題


* **callback 的問題：可靠性不足**

為了解決這個問題，ES6 新增了新語法 Promise。

因為 Promise 是為了解決上述的兩大問題所以他當然有以下兩個特點：

- **可靠**

  Promise 回來的東西是可信任的，一旦回傳就無法被更改。


- **控制回歸**

  將執行 callback 的控制權回歸自己的程式。



## 參考

[You Don't Know JS: async & performance](https://github.com/getify/You-Dont-Know-JS/tree/master/async%20%26%20performance)

http://www.ghostchina.com/promises-the-sync-problem-part-1/



### 控制的反逆轉

### 可靠性

* 時機
* 次數
* 拒絕波動拳

