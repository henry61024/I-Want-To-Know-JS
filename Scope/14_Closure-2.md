# JavaScript Closure (2)

這篇中，我們要加深大家對 Closure 的觀念。

在[上一篇](https://ithelp.ithome.com.tw/articles/10195343)，我們提到要觀察到 Closure 有幾個條件：

- 有巢狀 Scope 的存在
- 傳遞了被包裹的 Function
- 被傳遞的 Function 在原有的 Scope 以外被執行

藉由這樣，我們才能在外部的 Scope，查找被包裹的 Function 的外層 Scope 環境。

接著我們要證明，其實 Closure 在 JavaScript 中隨處可見。



## Closure 隨處可見

基本上只要傳遞了 Funciton，十之八九就有 Closure 產生，就讓我們用大家常常使用的 `setTimeout` 來當作範例：

```javascript
function asyncLog(message) {
  setTimeout(function() {
    console.log(message);			// "logged after one second"
  }, 1000);
}
asyncLog('logged after one second');
```

相信大家都很清楚以上的範例程式會有甚麼行為。當我們執行 `asyncLog` 之後，他會在一秒後印出 `"logged after one second"`。看似理所當然，但有發現這裡面有 Closure 存在嗎？

讓我們看看 `setTimeout`，`setTimeout` 能吃兩個參數：一個是 callback，也就是延遲後要做的 Function、一個是delay，也就是延遲時間，因此我們可以把 `setTimeout` 簡單看成是這樣：

```javascript
function setTimeout(callback, delay)  {
  // wait for delay
  // ...
  callback();
}
```

就是 `callback` 在等待了一段時間後被執行。

再回頭看一下 `asyncLog` 的 `setTimeout`，他傳入了一個匿名函式，這個匿名函式中要做的事很簡單，就是印出 `message`。那問題來了，一秒後 `asyncLog` 早就已經被執行完了，理論上`asyncLog` 的 Scope 早就已經被消滅，為什麼還能印出 `message` 呢？

沒錯，就是透過 Closure。`setTimeout` 中的 匿名函式的 Scope，在被指派給 `callback` 這個參數，在等待了一秒後 `callback()` 執行，匿名函式的 Scope 此時被建立出來了，為了尋找 `message` ，JavaScript 從 匿名函式的 Scope 往上找到 `ayncLog` 的參數 `message`，而裡面的值就是我們的答案 `"logged after one second"`！

讓我們對照一下觀察到 Closure 的條件：

- 有巢狀 Scope 的存在？ 

  有，就是 `setTimeout` 中的匿名函式。

- 傳遞了被包裹的 Function？

  有，匿名函式被指派給 `setTimeout` 中的參數 `callback`。

- 被傳遞的 Function 在原有的 Scope 以外被執行？

  有，因為匿名函式被傳遞給參數 `callback` 後就在 `setTimeout` 的 Scope 中被執行了，而不是在原來 `asyncLog` 的 Scope 中執行。

這個範例符合了我們講的觀察到 Closure 的條件，而正是因為 匿名函式被傳遞到了外部，因此讓我們可以觀察到 Closure。

請記住，**Closure 是隨處可見的，只要有 Function 被傳遞的行為發生，十之八九就可以觀察到 Closure**。



## Closure 額外觀念釐清

Closure 非常常見且非常實用的，為了讓大家有更正確的觀念，接下來，我們會再用一個範例來釐清 Closure。

### `setTimeout` 與 `for`

今天我們想要在延遲一秒後印出 0 ~ 4，讓我們來看看這個範例能不能達到我們的要求：

```javascript
for(var i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i);				// 5 5 5 5 5
  }, 1000);
}
```

這個範例的答案其實是在一秒後印出五個 `5`，感到驚訝嗎？

其實原因很簡單，在這個範例中，`setTimeout` 隨著 `for` 被執行了五次，此時 `i` 已經是 `5` 了，等待一秒之後，傳入 `setTimeout` 的五個匿名函式都被執行，並且往外查找 `i`。理所當然的這五個匿名函式都會查找到同一個 `i`，這個 `i` 的內容是 `5`，所以最後的答案自然就會是 `5 5 5 5 5`。

那要如何印出預期中的 `0 1 2 3 4` 呢？只要我們能夠讓匿名函式往上查找到一個變數，而這個變數可以保持當下 `i` 值的變數就好，那我們就成功了。

```javascript
for(var i = 0; i < 5; i++) {
  (function(i) {
    setTimeout(function() {
      console.log(i);			// 0 1 2 3 4
  	}, 1000);
  })(i);
}
```

這個解法中，每次迭代中，我們都會建立出一個新的 IIFE，並且將當下迭代的 `i` 值當作參數傳入 IIFE 中，換句話說就是，這些 IIFE 的 Scope 中記錄著當下迭代的 `i` 值。當匿名函式被 `setTimeout` 執行而往外層查找 `i` 時，就會拿到每次迭代的 `i` 值了，因此我們就可以印出 `0 1 2 3 4`。



## 小結

在本篇，我們用範例來釐清 Closure 的觀念。

Closure 是隨處可見的，基本上只要傳遞了 Funciton，十之八九就有 Closure 產生，例如 `setTimeout` 的第一個參數是要傳入 Function，等待一秒後在執行，這就是一個很好的 Closure 例子。



## 參考

[You Don't Know JS: Scope & Closure](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)