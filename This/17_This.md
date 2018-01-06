# JavaScript This

在 `C++` 與 `Java` 這類的強行別語言中，this 往往代表著物件本身。我們在這些語言中操作 this 時，就整於是直接操作物件本身。換句話說，this 在**這些語言**中是**編寫時期就決定好了**，因為使用 this 就是使用該物件。

對於 JavaScript 來說，this 就不是那麼直覺的東西了，他真正的內容是 **取決於 Function 的執行情境與方式**，也就是 **JavaScript 中的 this 是執行時期決定**的，因為執行 Function 的方式不同，也會連帶影響到 Function 中的 this 指向的內容。

在 You don't know JS 中，詳細的說明了在執行了 Function後，JavaScript this 的五種繫結：

* 預設繫結
* 隱含繫結
* 明確繫結
* new 繫結
* 語彙繫結

接下來我們將依序對這五種規則詳細的解釋。



## 預設繫結

### 套用情境

先讓我們來看看什麼時會會套用預設繫結。大致上分為兩種：

* 當我們用最陽春的方式 **單獨呼叫函式** 的時候，就會套用預設繫結。

  什麼意思呢？就是他不是被當作物件的方法調用，且只使用 `()` 執行此 Function 的話，此 Function 中的 this 就會套用預設細節。

* 當我們刻意忽略 this 的時候

  如上一張所提，當我們使用 `apply`、`call` 執行 Function，而第一個參數卻設為 `null` 的時候。

那到底套用預設繫結的 this 內容會是甚麼呢？

### this 的內容

這時我們許久未見的 `use strict` 又作為分隔線登場了！

* **在非嚴格模式下，this 的內容就會是 global 物件**
* **在嚴格模式下，this 的內容就會是 `undefined`**

講了那麼多，是不是還是不清楚呢？那當然就要上範例囉！

### 範例

首先來個最簡單的範例吧：

```javascript
var myString = 'hello global';
function logMyString() {
  var myString = 'hello local';
  console.log(this.myString);
}
logMyString.myString = 'hello function';

logMyString();						// "hello global"
```

在這個範例中，我們特地在 global 變數、function 屬性、function 內變數 都設了一個 `myString`，這時當我們執行 `logMyString` 時，究竟 `logMyString` 的 `this` 是指向哪呢？

#### 錯誤的觀念

如果我們用其他語言的觀念來推測 `this` 的內容的話，一定會不假思索的回答 `"hello function"` 吧！因為我們很直覺的覺得會認為 `this` 就代表我們當下執行的 Function `logMyString` 本身。

又或者有人回答 `"hello local"`，因為把 this 連結到 Scope 本身好像也是一個很合理的概念，但很可惜的，我們是不可能透過 JavaScript 中的 this 去找到任何的 Scope 變數的。

讓我們來看看 console 中執行看看結果會是什麼吧。挖，答案竟然是 `"hello global"`。

#### 利用套用情境推理

現在就讓我們使用剛剛提到的預設繫結規則來推理吧，`logMyString()` 到底符不符合預設繫結的套用規則呢？

* `logMyString` 不是被當作物件的方法被調用的
* 使用最陽春的 `()` 來執行 Function

Bingo！完全符合我們的預設繫結規則，答案就是 `"hello global"`

這時，相信有人發現，global 中的 `myString` 不就是一個 global 變數嗎，跟剛剛說的 `不可能透過 JavaScript 中的 this 去找到任何的 Scope 變數` 完全不一樣啊？這時就要該到複習時間了，[在全域變數宣告的變數會變成 global 的屬性](https://ithelp.ithome.com.tw/articles/10193821)。因此，我們應該更精準一點的認清，我們能找到是因為 `myString` 是 global 下的屬性，而不是因為它是 global Scope 中的變數。

#### 使用嚴格模式

確定在非嚴格模式下符合我們對預設繫結的預期了，接下來看看嚴格模式中的預設繫結是不是也如我們所想的那樣：

```javascript
'use strict';
var myString = 'hello global';
function logMyString() {
  console.log(this);				// undefined
  console.log(this.myString);		// Uncaught TypeError: Cannot read property 'myString' of undefined
}
logMyString.myString = 'hello function';

logMyString();
```

沒錯，這符合我們的預期 **在嚴格模式下，this 的內容就會是 `undefined`**。

### 保持使用嚴格模式

從例子中可以得知，當 this 是非嚴格模式下的預設繫結時是能夠修改 global 物件內容的，再加上使用 this 很有可能就不小心套用到預設繫結，為了不汙染 global Scope，在操作 this 的狀況下，保持使用嚴格模式是很重要的！



## 隱含繫結

緊接著介紹第二個繫結 - 隱含細節

### 套用情境

隱含繫結的套用情境非常簡單：

* **被當作物件的方法執行** 時，就會套用隱含繫結

那隱含繫結的 this 內容是甚麼呢？

### this 的內容

- 最終擁有方法的物件

接下來進到範例的階段

### 範例

```javascript
var myString = 'hello global',
    innerObj = { log: logMyString, myString: 'hello inner object' },
    obj = { log: logMyString, myString: 'hello object', innerObj: innerObj };
function logMyString() {
  console.log(this.myString);
}
logMyString.myString = 'hello function';

logMyString();						// "hello global"
obj.log();							// "hello object"
obj.innerObj.log();					// "hello inner object"
var logMyString2 = obj.log;
logMyString2();						// "hello global"
```

這裡與上一個範例程式大致上一樣，就讓我們直接跳到 `obj.log();` 來看看執行結果是不是如我們預期。

#### 利用套用情境推理

回顧隱含繫結的規則：**被當作物件的方法執行時，就會套用隱含繫結** 。此時的 `logMyString` 是作為 `obj` 的方法使用，完全符合隱含繫結的規則，而擁有這個 `log` 這個方法的物件 `obj` 就自然會是 this，所以 `obj.log()` 的答案就會是 `"hello object"`。

接著再進一步探討一下執行 `obj.innerObj.log();` 後，this 的內容。在這句中，實際上*最終擁有* `.log` 這個方法的物件是 `innerObj`，也就代表此時的 this 是 `innerObj` 而非  `obj`，因此答案會是 `hello innerObj`。

#### 對比預設繫結

最後我們將 `obj.log` 這個方法指派給 `logMyString2`，緊接著以 `logMyString2()` 執行。此時我們**要套用的規則會是預設繫結**，因為它符合 *用 `()` 單獨呼叫函式* 的規則，`logMyString2` 不會因為他曾經被當作方法執行過所以就永遠都套用隱含繫結。

會有這個錯覺是因為我們以為只要 Function 曾經被當作一個方法使用的話，這個 Function 就會永遠屬於物件的了。但當我們更深入的利用 [Call by sharing](https://ithelp.ithome.com.tw/articles/10194299) 的觀點來看時，會知道**方法其實只是代表一個屬性內存著 Function 的記憶體位置**，並不代表這個 Function 真正為物件所擁有。因此如果我們把方法指派給另一個變數，也就只是這個方法跟被指派變數同時都指向同一個 Function 的記憶體位置，如此而已，被指派變數也當然就不會知道目前指向的 Function 曾經被當作方法執行過。

回到範例，在執行 `logMyString2()` 時，當然會套用預設繫結，因為這個 Function 現在就是被存在一個變數中單獨執行，已經不是當初 `obj.log` 中，被當作方法執行的情境了！因此此時我們的 this 會是代表 global，答案又會回到 `"hello global"`！



## 小結

今天我們概述了 this，JavaScript 中的 this 取決於 Function 的執行情境與方式。

在 You Don't Know JS 中，歸類出五個 this 的規則，而本篇中先講解了其中兩個：

**預設繫結：**

* 套用情境：

  使用 `()` 單獨呼叫函式時

* this 內容：

  - 在非嚴格模式下，this 的內容就會是 global 物件
  - 在嚴格模式下，this 的內容就會是 `undefined`

**隱含繫結：**

- 套用情境：

  被當作物件的方法執行時

- this 內容：

  最後指向並執行 Funtion 的物件

另外，我們知道使用 this 時很容易不小心操作到預設繫結的 this，這時候就很可能會汙染到 global 物件，因此保持使用嚴格模式是很重要的。



## 參考

[You Don't Know JS: this & object prototypes](https://github.com/getify/You-Dont-Know-JS/tree/master/this%20%26%20object%20prototypes)









