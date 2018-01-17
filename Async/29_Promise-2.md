# JavaScript Promise (2)



## Promise 的意義



## Promise 測試

Promise 內一定要 `resolve` 或 `reject` 才能往下執行

`resolve` 多次

`reject` 多次



```javascript
var a = new Promise(function(resolve, reject) {
			if (true) resolve(123);
		})
console.log(a);		// Promise {<resolved>: 123}
a.then(...)			// can seperate then multiple times
a.then(...)
```





```javascript
var a = new Promise(function(resolve, reject) {
			if (true) reject();
		})
console.log(a);		// Error: Uncaught (in promise)
```





```javascript
var a = new Promise(function(resolve, reject) {
			if (true) return;
		})
console.log(a);		// Promise {<pending>}
```



![../Image/29_Promise-2/Promise-2-1.png](../Image/29_Promise-2/Promise-2-1.png)



## then 的測試 & Promise Chain

不用 resolve，因為他不是 Promise

就像在執行一般函式一樣，有回傳東西就會包裹成 Promise，帶到 Promise chain

兩個參數，resolve & reject



```javascript
var a = new Promise(function(resolve, reject) {
			if (true) resolve(123);
		})
.then(getAsyncValueWithPromise)
.then(printValue)
console.log(a);		// Promise {<resolved>: 123}

function printValue(value) {
	console.log('returned value:', value);
}

function getAsyncValue(value) {
  setTimeout(printValue.bind(null, value), 1000);
}

function getAsyncValueWithPromise(value) {
  return new Promise(function(resolve, reject) {
    setTimeout(function() {
    	printValue(value);
        resolve();
    }, 1000);
  })
}
```





## catch

`catch` 是抓整條 chain 上的錯誤，而非單一 promise/then 上的錯誤

抓單一 then 上的錯誤要使用 then 的第二個參數

```javascript
var a = new Promise(function(resolve, reject) {
			if (true) reject('oops');
}).catch(function(e) {
  console.log(e);
})
console.log(a);		// Promise {<resolved>: undefined}
```





## Promise 其他函式

`Promise.resolve()`

`Promise.reject()`

`Promise.race([])`

`Promise.all([])`