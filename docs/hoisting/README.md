## 函数提升 和 变量提升 以及 两者的优先级
> 今天群里有人讨论 `函数提升` 和 `变量提升` 的点, 所以总结了一下.
### 目标
- 变量提升
- 函数提升
- 两者优先级
### 变量提升 `hoisting`
先看一下 `JavaScript引擎` 的工作方式:
1. 先解析代码, 获取所有被声明的 `变量`.
2. 然后逐行运行.

所以也就造成了, 所有的变量的声明语句, 都会被提升到代码头部, 这就是变量提升.
```js
console.log('第1次打印: ' + a);
var a = 1;
console.log('第2次打印: ' + a);
```
上面代码先 `console` 输出 `a` 的值, 此时 `a` 还没有被声明和赋值. 所以, 这种做法错误, 但是实际不会报错, 正是因为变量提升的作用, 真正运行的步骤如下:
```js
var a;
console.log('第1次打印: ' + a);
a = 1;
console.log('第2次打印: ' + a);
```
打印结果:  
`第1次打印: undefined`  
`第2次打印: 1`  
> 第1次输出 `undefined` 表明已经被声明, 但还未被赋值.  
第2次输出 `1` 表明已经被声明和赋值.  
==注意: 变量提升只对 `var` 命令声明的变量有效( `函数字面量式` 除外), 如果一个变量不是用 var 声明的, 就不会发生变量提升.== 

如下几种就不会发生提升:
```js
console.log(b);
b = 1;
```
打印结果: `Uncaught ReferenceError: b is not defined`  
> 解释: 变量 `b` 未声明, 这是因为 `b` 不是用 `var` 命令声明的, `JavaScript` 引擎不会将其提升，而只是视为对顶层对象的 `b` 属性的赋值.
```js
console.log(c);
let c = 1;
```
打印结果: `Uncaught ReferenceError: Cannot access 'c' before initialization`
```js
console.log(d);
const d = 1;
```
打印结果: `Uncaught ReferenceError: Cannot access 'd' before initialization`
> 解释: `c 和 d` 涉及到 ES6 的 `let` 和 `const` 关键字的变量声明. 使得js也有了 `块级作用域`, 使用 `let` 和 `const` 不会发生变量提升.  
### 函数提升
`函数提升` 可以细分为:
- 函数名的提升
- 函数内部的变量提升  

创建函数又有两种方式:
- 函数声明式
  - 采用 `function` 命令声明函数. 如: `function a () {}`
- 函数字面量式
  - 采用 `赋值语句声明` 函数. 如: `var a = function () {}`
##### 函数名的提升  
> `JavaScript 引擎` 将函数名视同变量名, 所以采用 `function` 命令( `函数声明式` )声明函数时, 整个函数会像变量声明一样, 被提升到代码头部.  
所以下面的代码不会报错:
```js
f();
function f () {}
```
> 解释: 上面代码看上去, 在声明之前就调用了 `f()` , 但由于 `函数提升` 的作用, `f()` 被提升到了代码头部, 也就是说在调用之前已经被声明了.   
上面代码实际执行顺序为:  
```js
function f () {}
f();
```
但是, 如果采用 `函数字面量式` 定义函数会怎样呢?
```js
f();
var f = function () {};
```
执行结果: `Uncaught TypeError: f is not a function`  
上面的代码实际执行顺序为:
```js
var f;
f();
f = function () {};
```
> 跟变量提升一样, 代码第二行调用 `f` 时, `f` 只是被声明了, 并没有被赋值, 等于 `undefined`, 所以会报错.  
###### 小扩展
> 如果同时采用function命令和赋值语句声明同一个函数，最后总是采用赋值语句的定义.  
```js
f() // 2
var f = function() {
  console.log('1');
}
function f() {
  console.log('2');
}
f() // 1
```
##### 函数内部的变量提升
> 与全局作用域一样, 函数作用域内部也会产生 `变量提升` 的现象. `var` 命令声明的变量, 不管在什么位置, 都会被提升到函数体的头部.  
举个栗子:
```js
function foo (x) {
    if (x > 100) {
        var tmp = x - 100;
    }
}
```
上面的代码等同于: 
```js
function foo (x) {
    var tmp;
    if (x > 100) {
        tmp = x - 100;
    }
}
```
### 函数提升 和 变量提升的优先级
```js
console.log('a第1次: '+a);
console.log('a()第1次: '+a());
var a = 3;
function a() {
    return 10;
}
console.log('a第2次: '+a)
a = 6;
console.log('a()第2次: '+a());
```
上面代码实际执行顺序为:
```js
function a() {
    return 10;
}
var a;
console.log('a第1次: '+a);
console.log('a()第1次: '+a());
a = 3;
console.log('a第2次: '+a)
a = 6;
console.log('a()第2次: '+a());
```
执行结果:  
`a第1次: function a() {
            return 10;
        }
`  
`a()第1次: 10`  
`a第2次: 3`  
`Uncaught TypeError: a is not a function`
>解释: 从上面代码执行的结果分析  
由于 `函数提升` 的作用, `function a()` 被提升到代码头部;  
由于 `变量提升` 的作用, `var a = 3;` 变量 `a` 被提升到 仅次于 `a()` 方法的后面. 所以有了以下执行步骤:  
`a第1次` 打印出方法体;  
`a()第1次` 打印 `10`, 因为调用了 `a()` 方法, 此方法被提升到了 `代码作用域头部`;  
`a第2次` 打印 `3`, 因为此时 `变量a` 被赋值为 `3`;  
`a()第2次` 打印 `未定义错误` 这里是因为 `变量a` 被赋值为 `6` 了;  
> 由执行结果可知, `函数提升` 的优先级, 高于 `变量提升` 的优先级, 且不会被 `变量声明` 覆盖, 但会被 `变量赋值之后` 覆盖.  
### 练习 
案例1:
```js
f1();
f2();
function f1 () {
    console.log('f1')
}
var f2 = function () {
    console.log('f2')
}
``` 
上面代码实际执行顺序为: 
```js
function f1 () {
    console.log('f1')
}
f1();
var f2;
f2();
f2 = function () {
    console.log('f2')
}
```
打印结果:  
`f1`  
`Uncaught TypeError: f2 is not a function` 

案例2:
```js
(function() {
　　console.log(a);
　　a = 'aaa';
　　var a = 'bbb';
　　console.log(a);
})();
```
上面代码实际执行顺序为: 
```js
(function() {
    var a;
　　console.log(a);
　　a = 'aaa';
　　a = 'bbb';
　　console.log(a);
})();
```
打印结果:  
`undefined`  
`bbb`

### 小结
- 变量提升
  - 所有的变量的声明语句, 都会被提升到代码头部.
  - 变量提升只对 var 命令声明的变量有效.
  - 如果一个变量不是用 var 声明的, 就不会发生变量提升.
- 函数提升
  - 函数名的提升
    - 采用 function 命令声明函数时, 整个函数会像变量声明一样, 被提升到代码头部.
    - 如果同时采用 `function命令` 和 `赋值语句` 声明同一个函数，最后总是采用 `赋值语句` 的定义.
- 优先级: 函数提升 > 变量提升
  - `函数提升` 不会被 `变量提升` 覆盖, 但会被 `变量赋值之后` 覆盖.