# JavaScript Closure

我想 Closure 應該算是讓初學者進入 JavaScript 對難理解確也是最重要的一個概念，想當初剛開始學習 JavaScript 的後，看到 Function 中又可以再宣告 Function，只覺得一片混亂而已。

Function 中為何能再宣告 Function 這點已經在 [函式篇](https://ithelp.ithome.com.tw/articles/10194538) 介紹過，接下來，我們將利用累積下來的 Function 與 Scope 概念去挑戰 Closure。



## Closure 是什麼

*能夠傳遞 Scope，並允許 Scope 在原有位置以外取用的能力即為 Closure*

首先讓我們看看範例：

```javascript
function testClosure() {
  var inner = 'inner';
  function getInner() {
    return inner;
  }
  return getInner;
}

var innerFn = testClosure();
console.log(innerFn());			// inner
```

有看出 `console.log(innerFn())` 的答案是 `"inner"` 嗎？

看到現在你可能覺得理所當然，又或者不知所措，無論如何，讓我們看看這個範例程式裡有哪些特殊的地方：

### 巢狀 Scope
到了複習的時刻了

* 回想一下 JavaScript 中的 Scope 最常見的單位是什麼？是 Function
* 巢狀 Scope 是什麼意思？

我們可以看到這個範例裡有巢狀 Scope，因為函式 `testClosure` 包裹著函式 `getInner`。更精準的說，應該是當 `getInner` 被執行時，就會產生巢狀 Scope，畢竟 Function 也是要被執行之後，才會有自己的 Scope。

### 傳遞了被包裹的 Function，並在原有的 Scope 以外執行
* 接著，Function 是 JavaScript 的 First-class Object，也就是 Function 在 JavaScript 中是允許跟 Object 一樣被傳遞的。

從範例來看，函式 `getInner` 被傳到了外部，指派給變數 `innerFn`，隨後就 `innerFn()` 執行 Function。

現在有個問題：如果現在被包裹的 Function ( `getInner` )試圖取用包裹他的外層 Scope ( `testClosure()` ) 的話，會發生什麼事呢？

接下來就是我們最後，也是最重要的一部分了。

### 允許 Scope 在原有位置以外取用

先讓我們談談所謂的取用 Scope 是什麼意思，在這裡，所謂的取用是指：不但可以取用自己的 Scope，也可以利用 Scope Lookup 去尋找到他的父 Scope

取用的意思是什麼？Scope Lookup

Scope Lookup 就像指標

Scope 包裹 + Scope Lookup

如果一執行完 Function 就刪除 Scope 的話，外部就沒辦法取用了

實際上的重點就只是多了一個傳遞 Scope 的能力，再利用 Scope Lookup 去查找被傳遞 Function 的 parent scope 的內容

看的到目前 Scope 看不到的內容

在外部執行 Scope









圖

![../Image/13_Closure/closure-1.png](../Image/13_Closure/closure-1.png)



定義



觀察到的條件



Closure 無處不在



範例：setTimeout, for



Closure 的空間問題



傳遞 Scope 能達成的黑魔法：模組，附註可以參考 You don't know JS