# JavaScript 非同步執行

今天我們要來講的事 JavaScript 的非同步執行，那何為非同步執行呢？

先別急，在了解非同步執行之前我們應該先了解何謂同步執行。



## 同步執行 ( Scynchronous )

同步處理就像是一般的程式邏輯的話，都是按照你所寫的一行一行去執行，而順序必定都是**前一個動作執行完才會去接著執行下一個動作**。

讓我們用去速食店買漢堡來舉個例子，我們總共會有三個步驟：

* 買漢堡
* 吃漢堡
* 倒廚餘

假如我們把這個行為寫成一個同步程式的話，會像這樣：

```javascript
var buger = buyBuger(),
    junk = eatBurger(buger);
throwTheJunk(junk);

function buyBuger() {
  console.log('buy burger');
  return { bread: 'bun', vegetable: 'lettuce', meat: 'beaf' };
}
function eatBurger(buger) {
  console.log('eat burger ' + buger.bread + ' ' + buger.vegetable + ' ' + buger.meat);
  return { waste: 'lettuce' };
}
function throwTheJunk(junk) {
  console.log('throw ' + junk.waste);
}

// Output:
// "buy burger"
// "eat burger bun lettuce beaf"
// "throw lettuce"
```

就像我們描述的一樣，因為程式是照順序一行行執行下來的，因此我們就照著

* `buyBuger()`
* `eatBuger()`
* `throwTheJunk()`

這個順序執行，一切看起來都很不錯，只是現在等餐的時間沒事做會有點無聊。

那就讓我們更擬真一點。等店員做漢堡、吃漢堡 都是要時間的，而做後面一個步驟都要等前面的步驟做完之後才能進行 ( 畢竟漢堡都還沒買到手要怎麼吃呢！ )，我們要怎麼模擬這種情境呢？



## 非同步執行 ( Ascynchronous )

接下來就輪到我們的 非同步執行 出場拉。

何為非同步執行？簡單來講就是 **等待前面步驟執行完之前可以先去執行後面的步驟**。

換成剛剛的例子來說明，因為 等店員做漢堡、吃漢堡 都是要時間的，你總不會希望點完餐到取餐為止都一定要直愣愣的站在那邊甚麼都不行做吧，一定會希望在等待點餐的時間還是可以做些其他事情才不會浪費時間。

所以現在我們要多做一件額外的事：

* 滑手機

現在我們要執行的目標是，與買漢堡相依的步驟等到拿到漢堡後再做 ( 吃漢堡、倒廚餘)，但可以先做其他不相關的步驟 ( 玩手機 )

加入 `playMobile()` 之後，我們的程式會變成這樣：

```javascript
buyBuger(function(buger) {
	eatBurger(buger, throwTheJunk);
});
playMobile();

function buyBuger(callback) {
  console.log('buy burger');
  setTimeout(function() {
    callback({ bread: 'bun', vegetable: 'lettuce', meat: 'beaf' });
  }, 1000);
}
function eatBurger(buger, callback) {
  console.log('eat burger' + buger.bread + ' ' + buger.vegetable + ' ' + buger.meat);
  setTimeout(function() {
  	callback({ waste: 'lettuce' });
  }, 1000);
}
function throwTheJunk(junk) {
  setTimeout(function() {
    console.log('throw ' + junk.waste);
  }, 1000);
}
function playMobile() {
  console.log('play mobile');
}

// Output:
// "buy burger"
// "play mobile"
// "eat burger bun lettuce beaf"
// "throw lettuce"
```

在這裡，我們用 `setTimeout` 來模擬等待的時間，每個步驟的等待都需要一秒，當我們執行完當個步驟後，就會去呼叫 `callback` 來執行下一個步驟。而在等待時間中，我們就先繼續往下做其他沒有相依性的步驟，這就是非同步。

可以看到，現在我們的動作變成：

* 買漢堡
* 等待時玩手機
* 漢堡終於做好了，開始吃
* 吃完漢堡，丟廚餘

總算是完美了，現在我們等餐時也可以滑手機了。

### 非同步的意義

非同步的意義在於不讓等待時間 block 住其他不相關動作的執行。

### 非同步執行 vs 同步執行

試想，假如今天你採取同步執行要抓取一個網頁的資料，而抓完資料後才能進行下一個步驟，那這樣一定很令人沮喪。當這個網頁不小心掛點了，你的程式就停在那邊了。

相較之下，若我們採用非同步執行，等待網頁時我們就繼續做後面不相關的步驟。就算今天這個網頁掛點了，我們可以不理會他繼續做其他沒有相依性的事情就好。

### 非同步執行 vs 多執行緒

也許有人會想說，非同步不就是多開一個 thread 嗎，程式怎麼會被 block 住呢？很可惜的，非同步 與 多執行緒 是不同的，而 JavaScipt 也只有 single thread 而已，因此不會有多執行緒這檔事。

JavaScript 中的非同步執行其實只是遇到要等待的事情時，接續去執行後續的述句而已。



## 小結

這篇主要講的是非同步執行，非同步執行的意思就是：

* 等待前面步驟執行完之前可以先去執行後面的步驟

而非同步最大的意義是

* 不讓等待時間 block 住其他不相關動作的執行

要注意的是 非同步執行 與 多執行緒 是不同的。而 JavaScript 中也只有 single thread，因此不會有多執行緒這檔事。



## 參考

[You Don't Know JS: async & performance](https://github.com/getify/You-Dont-Know-JS/tree/master/async%20%26%20performance)

http://www.ghostchina.com/promises-the-sync-problem-part-1/