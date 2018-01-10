# JavaScript Object-Oriented (3) - inheritence



## 推理



## 錯誤的繼承

```javascript
Foo.prototype = Bar.prototype;
```



```javascript
function Animal(name, gender, age) {
  this.name = name;
  this.gender = gender;
  this.age = age;
}
Animal.prototype.speak = function() {
  console.log('some sounds');
};
Animal.prototype.move = function() {};

function Dog(name, gender, age) {
  this.name = name;
  this.gender = gender;
  this.age = age;
}
Dog.prototype = Animal.prototype;
Dog.prototype.speak = function() {
   console.log('Bow-wow');
};
Dog.prototype.move = function() {
   console.log('walk');
};
var dog1 = new Dog('Blacky', 'male', 3),
    dog2 = new Dog('Whity', 'female', 1),
    animal = new Animal('Browny', 'male', 5);
dog1.speak();					// "Bow-wow"
dog2.speak();					// "Bow-wow"
animal.speak();					// "Bow-wow"
```





## 正確的繼承

### 繼承屬性

```javascript
function Dog() {
  Animal.apply(this, arguments);
  // this.xxx
}
```







### 繼承原型方法

### new

```javascript
Dog.prototype = new Animal();
```

![../Image/22_Object-oriented-3/Object-oriented-3-2.png](../Image/22_Object-oriented-3/Object-oriented-3-2.png)



### Object.create

polyfill?

![../Image/22_Object-oriented-3/Object-oriented-3-1.png](../Image/22_Object-oriented-3/Object-oriented-3-1.png)

```javascript
Dog.prototype = Object.create(Animal.prototype);
```



### setPrototypeOf

```javascript
Dog.prototype = Object.setPrototypeOf(Dog.prototype, Animal.prototype);
```



### 修改完成後

```javascript
function Animal(name, gender, age) {
  // throw error
  this.name = name;
  this.gender = gender;
  this.age = age;
}
Animal.prototype.speak = function() {
  console.log('some sounds');
};
Animal.prototype.move = function() {};

function Dog(name, gender, age) {
  Animal.apply(this, arguments);
  // this.xxx
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.speak = function() {
   console.log('Bow-wow');
};
Dog.prototype.move = function() {
   console.log('walk');
};
var dog1 = new Dog('Blacky', 'male', 3),
    dog2 = new Dog('Whity', 'female', 1),
    animal = new Animal('Browny', 'male', 5);
dog1.speak();					// "Bow-wow"
dog2.speak();					// "Bow-wow"
animal.speak();					// "Bow-wow"
```







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