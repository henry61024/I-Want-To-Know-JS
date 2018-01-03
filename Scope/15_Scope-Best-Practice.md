# JavaScript Scope Best Practice

不論在任何語言中，只公開介面，而不公開實作細節都是非常重要的。我們並不會希望把所有的宣告、實作細節公開在 Global Scope 上讓大家取用，因為這樣不但會讓命名衝突的機率變大，而且使用我們的程式的人也可能因為誤用破壞原來預期的程式邏輯。

**只公開介面，而封裝私有細節**，這個概念我們把他稱作 **最小暴露原則**。

而在 JavaScript 中，肩負著達成最小暴露原則使命的就是 Scope。正確的操作 Scope 能讓我們的程式變得更清楚明瞭且更易於維護。

接下來，我會用打招呼機器人當作一個範例說明 Scope 的實作建議，並且利用這些建議，一路改善我們的程式，讓他變成一個符合我們要求的小型函式庫。



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
hello.us = 'goodbye ';
greetWithEnglish()
```



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
```



```javascript
var GreetBot = (function GreetBot() {
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
```





## 最小暴露原則

範例

衍伸出函式庫的概念

## 將函式包裹在 IIFE 裡

範例 * 2

### 模組

提揭露模組

範例