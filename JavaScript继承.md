# JavaScript 中的继承

js 中常见的 6 种继承方式

## 原型链继承

通过重写子 function 的 protoType，指向父 function 的实例。通过 protoType 完成继承。

```javascript
function Parent() {
  this.name = "father";
  this.age = 50;
  this.play = [1, 2, 3];
}

function Child() {
  this.name = "child";
  this.age = 10;
}

Child.prototype = new Parent();
```

存在问题：

```javascript
const a1 = new Child();
const a2 = new Child();

const b = new Parent();

console.log(a1, a2);

a1.play.push(2);

console.log(a1, a2);

```

当子函数创建一个实例对象后，修改了属性值，另外的实例上的属性值也会被改变。因为两个实例用的是同一个原型对象，**原型对象上的属性内存空间是共享的，不会随着实例的创建而单独隔开！当一个发生变化的时候，另外的也会随之变化，这是原型链继承的一个缺点！！**

## 构造函数继承

通过构造函数，借助call的方法改变this指向来实现继承。

```javascript

//首先创建一个父类
function Parent(){
	this.name = "father";
	this.age  = 40;
}

//父类原型上创建一些公共方法
Parent.prototype.getName = function (){
	return this.name
}

// 需要继承的子类
function Child(){
	Parent.call(this)
	this.type = 'child'
}

const child1 = new Child()
console.log(child1.age)
console.log(child1.getName())	//error

```

构造函数继承，子类可以拿到父类的属性，但是拿不到父类原型对象上之前定义的方法，如getName这种，子类就不能继承protoType上的内容。

**构造函数使父类的引用属性不会被共享，弥补了第一种原型链继承的缺点。问题是，只能继承到父类实例的属性和方法，不能继承prototype原型上的属性和方法！**


## 组合继承

组合继承是结合了前两种继承， 原型链继承 + 构造函数继承。

把两种写法结合一下

```javascript
function Parent () {
	// 定义一些给实例的属性
    this.name = 'parent';
    this.play = [1, 2, 3];
  }
//	在父类protoType上定义一些方法
  Parent3.prototype.getName = function () {
    return this.name;
  }

  function Child() {
    // 第二次调用 Parent()
    Parent.call(this);
    this.type = 'child';
  }

  // 第一次调用 Parent()
  Child.prototype = new Parent();
  // 手动挂上构造器，指向自己的构造函数
  Child.prototype.constructor = Child;
  var s3 = new Child();
  var s4 = new Child();
  s3.play.push(4);
  console.log(s3.play);  // 不互相影响
  console.log(s4.play);
  console.log(s3.getName()); // 正常输出'parent3'
  console.log(s4.getName()); // 正常输出'parent3'

```


## 寄生组合式继承（原生js最优解）

```javascript
function clone (parent, child) {
    // 这里改用 Object.create 就可以减少组合继承中多进行一次构造的过程
    child.prototype = Object.create(parent.prototype);
    child.prototype.constructor = child;
}

function Parent6() {
    this.name = 'parent6';
    this.play = [1, 2, 3];
}
Parent6.prototype.getName = function () {
    return this.name;
}
function Child6() {
    Parent6.call(this);
    this.friends = 'child5';
}

clone(Parent6, Child6);
    Child6.prototype.getFriends = function () {
    return this.friends;
}

let person6 = new Child6();
console.log(person6);
console.log(person6.getName());
console.log(person6.getFriends());

```
