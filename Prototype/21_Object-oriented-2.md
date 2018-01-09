

## 自訂子型別共用函式

那我們能不能讓一個子型別，像一開始提到的 Array 那樣有共用函式呢？當然可以。

### 較差的作法

相信很多人馬上就想到作法：

```javascript
function Dog(name) {
  this.name = name;
  this.speak = function() {
  	console.log('Bark');
  };
  this.move = function() {
  	console.log('walk');
  };
}
var dog1 = new Dog('Blacky'),
    dog2 = new Dog('Whity');
dog1.speak();					// Bark
dog2.speak();					// Bark
```

只要在建構式裡面，對新建的物件加入方法就行啦，這樣我們每次在建構 `Dog` 子型別的時候，建構式就會自動幫我們把共用的函式加上去了。

沒錯，這會是一種解法，也確實每個新建出來的物件都會被加上這個方法，但這個解法有個問題：它並不是真的共用的，它其實是每次建構時，都特地多加一個屬性上去，這樣當我們大量產生這種子型別的物件時，就會浪費許多的記憶體來存這些重複的程式碼。

### 使用 Prototype

那撇除以上的方法不談的話，我們還有什麼選擇呢？

有的，使用 `Prototype` 就可以達成真正的共用函式。

所謂的 Prototype 就是原型，在 JavaScript 中，只要是 **函式上面都有預設有 Prototype 屬性**，而這個屬性是公開的。當我們將一個 Function 當作建構式使用的時候，被新建出來的物件就可以透過 `[[Prototype]]` 參考連結到這個原型物件上。

沒錯，你並沒有看錯，所謂的原型是一個物件，而且所有新建出來的物件都是被連接到同一個原型物件上。並不像其他的語言一樣，每個類別藍圖中的函式、屬性等行為都會被拷貝到各個物件中。在 JavaScript 中，則是**各個同樣子型別的物件都以 `[[Prototype]]` 連結到同一個原型物件**。

那究竟我們要如何用 Prototype 寫出能夠讓多個物件共用的函式呢？

```javascript
function Dog(name) {
  this.name = name;
}
Dog.prototype.speak = function() {
   console.log('Bark');
};
Dog.prototype.move = function() {
   console.log('walk');
};
var dog1 = new Dog('Blacky'),
    dog2 = new Dog('Whity');
dog1.speak();					// "Bark"
dog2.speak();					// "Bark"

Dog.prototype.speak = function() {
   console.log('Bow-wow');
};
dog1.speak();					// "Bow-wow"
dog2.speak();					// "Bow-wow"
```

觀察一下範例，我們在 `Dog.prototype` 上面加上了方法，當我們建構式呼叫 `Dog` 創建出 `dog1` & `dog2` 等物件之後，這些物件就可以經由 `[[Prototype]]` 去找到原型物件 `Dog.prototype` 上。執行 `dog1.speak()` 時，由於 `dog1` 本身並沒有 `speak` 方法，因此 JavaScript 會通過 `[[Prototype]]` 去往上查找原型中是否有這個屬性，接著發現，沒錯 `Dog.prototype` 上有個 `speak` 屬性！當然執行後，結果就會印出 `"Bark"` 了。

根據上面的敘述，不管事幾個 `Dog` 子型別的物件，當我們執行 `speak` 的話，其實最後都會連結到同一個原型物件的方法，也就是說今天我們開了一個連鎖寵物店，有 `dog1` ~ `dog1000` 那麼多的狗，他們都還是會連結到同一個 `speak` 方法。

為了證明這點，讓我們試試看直接將 `Dog.prototype.speak` 改成印出 `"Bow-wow"`，看看會不會所有的 `dogX` 的叫聲都變成 `"Bow-wow"`。果然如我們所料，在改完原型函式之後、`dog1` 與 `dog2` 叫聲都變成 `"Bow-wow"` 了！

此時在觀察一下 `dog1` 跟 `dog2` 的物件在我們 Dev Tool 上看起來會長怎麼樣：

![../Image/21_Object-oriented-2/Object-oriented-2-1.png](../Image/21_Object-oriented-2/Object-oriented-2-1.png)

啊哈，終於出來了，現在的跟圖跟 Array 中一樣有共用函式了。

`__proto__`



因此看看 `dog2.speak()`，



Prototype 適用狀況















prototype vs proto











## Where are Built in function & properties?

has own property



## Get & Put



## Prototype Chain

rules

end of prototype chain





## constructor and 藍圖





## Prototype & proto

prototypes are object

only function has prototype



## Inherit from prototype