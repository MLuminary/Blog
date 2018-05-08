---
title: js函数基础
date: 2017-08-17 23:54:14
tags: 
  - js
  - function
category: "javascript"
---

## 函数的定义

函数其实就是一个封装一段代码段的**对象**，那函数名其实仅是用来引用函数对象的一个普通变量

写代码的时候我们避免不了要重复用一些代码，一直重复写很耗时，而且不美观也不利于维护，因此函数的出现就是来让代码重用，便于维护。

一段代码，可能被反复使用，可以定义为函数，然后调用函数来使用这段代码。

## 函数的创建

**function 声明**

```js
function 函数名(参数列表) {
  函数体;
  return 返回值;
}
```

**直接量声明**

```js
var 函数名 = function(参数列表) {
  函数体;
  return 返回值;
};
```

**用 new 创建**

因为 Function 是内置类型，本身有一个 Function 的构造函数，是内置类型，所以是可以 `new` 的

```js
var 函数名 = new Function("参数名1","参数名2",...,"函数体; return 返回值")
```

这里注意，参数和函数体都要用引号引起来，但是一般函数的创建都不会这样创建，以前面的两种为主，那其实前面两种的创建是存在一定的差别的，下面会提到

## 函数的参数

函数执行时必须的数据变量，它分为显示参数(Parameters)与隐式参数(Arguments)

### 显示参数

```js
function fun(name, id) {
  //函数体
}
```

这种直接传进来的 name 和 id 就是显示参数，也就是你能看到的。其实函数传递的参数就相当于在函数体内又声明了一个局部变量

```js
var i = 10;
function fun(i) {
  i++;
  console.log(i);
}
fun(i);
```

上面的代码，就相当于下面的代码

```js
var i = 10;
function fun() {
  var i = 10; //这个值就是传进来的参数的值
  i++;
  console.log(i);
}
fun(i); //在此处传入i
```

### 隐式参数

每个 JavaScript 函数内部都有一个对象 `arguments` 对象,其实是一个类数组的对象,它会自动接受所有传入函数的参数值。

```js
function func(          ) {
  //arguments[          ]
}
```

值得一说的是,`arguments` 有下标有长度，可以通过下标来获得传入的参数，比如 `arguments[0]` 就是第一个参数, `length` 就可以遍历这个类数组对象，但是，毕竟他不是数组，所以不能进行一些数组特有的操作，比如 `sort`

## 声明提前

在开始执行程序前,js 引擎会首先查找 `var` 声明的变量和 `function` 声明的函数，将其提前到当前作用域的顶部集中创建，而将赋值操作保留在原地,这里特别说一下,未用 `var` 声明的变量不会声明提前.

```js
console.log(a); //a is not defined
a = 10;
```

```js
console.log(a); //undefined
var a = 10;
console.log(a); //10
```

但是在他下面声明并赋值变量 `a`,因为声明提前,其实代码会变成下面这个样子

```js
var a;
console.log(a);
a = 10;
console.log(a);
```

这样看，一切都变得很合理。。

函数也是一样

```js
function fun() {
  console.log(1);
}
fun(); //2
function fun() {
  console.log(2);
}
fun(); //2
```

控制台会输出两个 2,因为 function 声明的函数也会声明提前,代码其实是下面这个样子

```js
function fun() {
  console.log(1);
}
function fun() {
  console.log(2);
}
fun(); //2
fun(); //2
```

第二次声明因为方法名字一样，后者覆盖了前者，所以再调用的时候就会调用最后这个

但是声明提前会增加程序解读的难度，因此我们在写程序时，尽量避免声明提前所带来的危害

### 声明提前的解决方法

那我们既然知道会有声明提前这种操作，就在变量和函数的声明时都放在当前作用域的顶部。

在 ES6 中 可以用 let 代替 var,不过要求在当前作用域中 let 变量之前不允许出现声明的变量

也可以用直接量声明变量的方法

```js
var fun = function() {
  console.log(1);
};
fun(); //1
var fun = function() {
  console.log(2);
};
fun(); //2
```

这种当然也会声明提前，那我们看一下声明提前后的代码

```js
var fun;
var fun;
fun = function() {
  console.log(1);
};
fun(); //1
fun = function() {
  console.log(2);
};
fun(); //2
```

声明提前但是赋值还是留在原地，所以虽然有声明提前，但是并不会改变我们原本想要的结果。也就解决了声明提前带来的危害。

## 匿名函数

函数创建时没有指定函数名

匿名函数使用后自动释放,会节约内存,他会划分临时作用域，避免全局变量污染全局。

### 用处

**callback**

将一个函数作为参数传入另一个函数内，被其他函数调用

举个栗子:chestnut:

```js
arr.sort(function(a, b) {
  return a - b;
});
```

```js
str.replace(/reg/g, function(kw,$1,$2,...){return 替换值})
```

#### 自调

定义函数后自己调用自己，调用结束后，立刻释放，不占内存

举个例子:chestnut:

```js
(function(参数列表) {
  函数体;
  return 返回值;
})(参数值列表);
```

会定义一个临时的作用域，减少使用全局变量，避免全局污染。

## 重载

相同函数名，不同参数列表的多个函数。在调用时，根据传入参数的不同，自动选择匹配的函数执行。

这样可以减少 api 的数量，减轻调用者的负担。

听起来很诱人，但是 js 语法不支持重载，原因是 js 不允许多个同名函数同时存在，后声明的函数会覆盖前面声明的。(哇。js 不支持你在这里说什么，神经病啊).

既然说重载，那肯定是可以通过某些方法实现的。这个方法就是利用 `arguments`

```js
function test() {
  if (arguments.length == 0) {
    //不传值的操作
  } else if (arguments.length == 1) {
    //传一个值进行的操作
  } else {
    //....等等
  }
}
```

其实也不是真正意义上的重载，因为并没有创建同名的多个函数，但是实现的效果是和重载差不多的。
