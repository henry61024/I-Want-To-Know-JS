# JavaScript Scope Best Practice

不論在任何語言中，只公開介面，而不公開實作細節都是非常重要的。我們並不會希望把所有的宣告、實作細節公開在 Global Scope 上讓大家取用，因為這樣不但會讓命名衝突的機率變大，使用我們的程式的人也可能因為誤用破壞原來預期的程式邏輯。

其中，**只公開介面，而封裝私有細節**，這個概念我們把他稱作  **最小暴露原則(Least Expolsure Principle)** 。

而在 JavaScript 中，肩負著達成最小暴露原則使命的就是 Scope。正確的操作 Scope 能讓我們的程式變得更清楚明瞭且更易於維護。

接下來，我會用打招呼機器人當作範例來說明 Scope 的實作建議，並且利用這些建議，一路改善我們的程式，讓他變成一個符合我們要求的小型函式庫。請注意在這些改善過程中，會提到一些關於 [Design Pattern](https://zh.wikipedia.org/wiki/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F) 的內容，但實際上本篇的主要目的還是在於討論 Scope 的實作建議。



## 陽春機器人

首先讓我們來講講這個打招呼機器人要有哪些功能：

* 用中文打招呼
* 用英文打招呼

就這樣，非常陽春的一個機器人。

如果是你，會怎麼實作呢？讓我們採用一個普通點的做法：

```javascript
var hello = {
        us: 'hello ',
        cn: '你好 '
      };
function greetWithEnglish(name) {
  console.log(getGreetingWords(name, 'us'));
}
function greetWithChinese(name) {
  console.log(getGreetingWords(name, 'cn'));
}
function getGreetingWords(name, language) {
  return hello[language] + name;
}
greetWithEnglish('henry');			// hello henry
greetWithChinese('henry');			// 你好 henry
hello.us = 'goodbye ';
greetWithEnglish('henry');			// goodbye henry
```

首先我們稍微介紹一下這個機器人的細節：

* `hello`：打招呼的語言集，裡面放有各國打招呼的用語。
* `getGreetingWords`：取得完整的打招呼句子，需要傳入打招呼對象的名字 & 決定要用什麼語言打招呼


* `greetWithXXX`：這系列的函式是主要使用的介面，會把打招呼的句子印給使用者看。

讓我們來測試測試他的功能，`greetWithEnglish` & `greetWithChinese` 都很好的運作。

然而有天，這個一個使用者對機器人隨意操作，不小心把 `hello` 的英文改成了 `goodbye` 了。OOPS，打招呼機器人變成說再見機器人了。

顯然我們不希望這種事情發生，那我們要怎麼做呢？



## 揭露模組 - 模組創造器

**改動重點：**用 Scope 封裝整個相關功能與內容，並只公布介面。

原本我們機器人就像一個沒裝殼的電腦一樣，由於使用者不清楚到底哪裡是操作介面，哪裡是實作細節，因此很容易把我們的機器人搞壞。

現在我們要做的事情就是幫機器人裝機殼，我只要把給使用者用的介面暴露，其餘使用者不需要知道的內部細節我們把它通通藏起來，這樣就會大大降低使用者亂操作導致機器人故障的機會了：

```javascript
function GreetBot() {
  var hello = {
        us: 'hello ',
        cn: '你好 '
      },
      interface = {
        greetWithEnglish: greetWithEnglish,
        greetWithChinese: greetWithChinese
      };
  function greetWithEnglish(name) {
	console.log(getGreetingWords(name, 'us'));
  }
  function greetWithChinese(name) {
	console.log(getGreetingWords(name, 'cn'));
  }
  function getGreetingWords(name, language) {
	return hello[language] + name;
  }

  return interface;
}
var greetBot1 = GreetBot();
greetBot1.greetWithEnglish('henry');			// hello henry
greetBot1.greetWithChinese('henry');			// 你好 henry
var greetBot2 = GreetBot();
greetBot2.greetWithEnglish('peter');			// hello peter
greetBot2.greetWithChinese('peter');			// 你好 peter
```

來看看我們做了什麼改動：

* **新增 `function GreetBot` 包裹整個機器人相關的功能與細節：**

  如此 Global 就無法隨意取用內部變數與函式。簡單來講就是我們幫機器人裝機殼了，使用者不能隨便對他內部上下其手。

* **新增 `interface` 物件：**

  `interface` 裡面裝載要公開的介面

* **回傳 `interface`：**

  每次呼叫 `GreetBot` 我們就會創建一個新的機器人來服務使用者。

現在，我們解決了使用者在不知情的狀況下改動到內部實作細節的問題，因為我們用 Function 把整個機器人**封裝**，只公布我們要提供的 `greetWithEnglish` & `greetWithChinese` 而已。

這種只公開介面，而隱藏實作細節的方式，我們稱之為 ***揭露模組***。

更精準的來講，我們所建立的 `GreetBot` 實際上是一個 ***模組創造器***，因為每次執行都會回傳一個 `interface` 回來。如果今天有一個新需求：老闆希望店裡只要有一個機器人就好，不然會很浪費記憶體空間，這樣的話，我們要怎麼實作呢？。



## 揭露模組 - 單一模組

**改動重點：**讓整個程式只會有一個介面。

因為我們希望店裡永遠只要有一個機器人就好，也就是我們只公開一次 `interface` 出去就好：

```javascript
var greetBot = (function() {
  var hello = {
        us: 'hello ',
        cn: '你好 '
      },
      interface = {
        greetWithEnglish: greetWithEnglish,
        greetWithChinese: greetWithChinese
      };
  function greetWithEnglish(name) {
	console.log(getGreetingWords(name, 'us'));
  }
  function greetWithChinese(name) {
	console.log(getGreetingWords(name, 'cn'));
  }
  function getGreetingWords(name, language) {
	return hello[language] + name;
  }

  return interface;
})();
greetBot.greetWithEnglish('henry');			// hello henry
greetBot.greetWithChinese('henry');			// 你好 henry
```

來看看我們做了什麼改動：

- **用 IIFE 取代原來的 `function GreetBot`。**

  因為我們**不再公布能夠產生並回傳 interface 出去**，**取而代之的是用 IIFE 只回傳一次 interface 給變數** `greetBot`，因此我們現在店裡就只會有一個機器人。

現在，因為店裡只有一個機器人，因此可以稱呼他為單一模組，這就有點類似 [Singleton Pattern](https://en.wikipedia.org/wiki/Singleton_pattern) 的概念。



## 小結

JavaScript 中，Scope 肩負著達成**最小暴露原則** 的使命，因此良好的設計是很重要的。

在這篇文章中，我們舉打招呼機器人這個範例，來說明如何利用 Scope 來讓程式 **只公開介面，並封裝私有細節**。

達成目的的手段，主要就是妥善裡用 Function 包裹整個相關功能，並且只回傳介面給外部，這樣我們的 Global Scope 就可以保持乾淨以降低程式出錯率並提高維護性。



## 參考

[You Don't Know JS: Scope & Closure](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)