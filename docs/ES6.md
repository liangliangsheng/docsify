## 1 Let和Const

### 1.1 Let

##### for循环中var和let

-  变量i是let声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量

```js
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 6
```

- `for`循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。

```js
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```

##### 不存在变量提升

##### 暂时性死区

- 在代码块内，使用`let`命令声明变量之前，该变量都是不可用的

```js
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```

- “暂时性死区”也意味着`typeof`不再是一个百分之百安全的操作。

```js
typeof x; // ReferenceError
let x;
typeof undeclared_variable // "undefined"
```

##### 不允许重复声明

### 1.2 块级作用域

##### 为什么需要

- 内层变量覆盖外层变量，变量提升   [var变量提升和重复申明](https://blog.csdn.net/weixin_30252709/article/details/99891205)
- 泄露为全局变量
- 匿名IIFE不再必要了

##### 块级作用域与函数

- ES5 规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。（浏览器支持）

```js
function f() { console.log('I am outside!'); }
(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }
  f();
}());

// ES5 环境  得到inside
function f() { console.log('I am outside!'); }
(function () {
  function f() { console.log('I am inside!'); }
  if (false) {
  }
  f();
}());
```

- ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。ES6 规定，块级作用域之中，函数声明语句的行为类似于`let`。
- ES6 浏览器中 (**避免使用**) 优先使用表达式
    - 允许在块级作用域内声明函数。
    - 函数声明类似于`var`，即会提升到全局作用域或函数作用域的头部。
    - 同时，函数声明还会提升到所在的块级作用域的头部。

```js
// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```
- 块级作用域中定义变量，一定要使用{}

### 1.2 Const命令
- 声明必须赋值
- 指向地址，对象可改变内部数据

### 1.3 顶层对象属性
- var命令和function命令声明的全局变量，依旧是顶层对象的属性
- let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。
```js
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```

## 2 变量解构赋值
### 2.1 数组结构赋值
##### 基本使用
```js
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []

let [x, y, z] = new Set(['a', 'b', 'c']);
x // "a"

function* fibs() {
let a = 0;
let b = 1;
while (true) {
  yield a;
  [a, b] = [b, a + b];
}
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
```
- 如果解构不成功，变量的值就等于undefined。
- 不完全解构，只匹配一部分的等号右边的数组，解构依然可以成功。
- 只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值 **Set**
##### 默认值
```js
let [foo = true] = [];
foo // true
let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'

let [x = 1] = [undefined];
x // 1
let [x = 1] = [null];
x // null

function f() {
  console.log('aaa');
}
let [x = f()] = [1];

let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```
- 当一个数组成员严格===undefined，默认值才会生效
- 如果默认值是一个表达式，惰性求值的，即只有在用到的时候，才会求值。
- 默认值可以引用解构赋值的其他变量，但该变量必须已经声明。
### 2.2 对象的解构赋值
##### 基本使用
```js
let { bar, foo } = { foo: 'aaa', bar: 'bbb' };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: 'aaa', bar: 'bbb' };
baz // undefined

let { foo: baz } = { foo: 'aaa', bar: 'bbb' };

const node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};
let { loc, loc: { start }, loc: { start: { line }} } = node;
line // 1
loc  // Object {start: Object}
start // Object {line: 1, column: 5}
```
- 解构失败，变量的值等于undefined。解构失败，变量的值等于undefined
- 变量名与属性名不一致，模式:变量名
- 同样可以嵌套，注意模式和变量名
- 对象的解构赋值可以取到继承的属性
##### 默认值  
```js
var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5

var {x: y = 3} = {};
y // 3

var {x: y = 3} = {x: 5};
y // 5

var { message: msg = 'Something went wrong' } = {};
msg // "Something went wrong"
```
- 类似数组的默认值
##### 注意点
```js
// 错误的写法
let x;
{x} = {x: 1};
// SyntaxError: syntax error

// 错误的写法
let x;
{x} = {x: 1};
// SyntaxError: syntax error
```
- {}被理解成代码块，用（）包裹
- 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。
### 2.3 字符串
```js
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
let {length : len} = 'hello';
len // 5
```
- 类数组对象可以结构
### 2.4 数值和布尔值
```js
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```
- 只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错
### 2.5 函数参数
```js
// 若不穿参数也能触发默认值
function move({x = 0, y = 0} = {}) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]

function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```
### 2.6 圆括号
```js
[(b)] = [3]; // 正确
({ p: (d) } = {}); // 正确
[(parseInt.prop)] = [3]; // 正确
```
- 赋值语句的非模式部分，可以使用圆括号
### 2.7 用途
##### 交换变量的值
```js
let x = 1;
let y = 2;

[x, y] = [y, x];
```
##### 函数参数的默认值
```js
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
```
##### 遍历Map结构
```js
for (let [key, value] of map) {
}
// 获取键名
for (let [key] of map) {
  // ...
}
// 获取键值
for (let [,value] of map) {
  // ...
}
```
## 字符串的扩展
### 3.1 字符串Unicode写法
```js
'\u007A' === 'z' // true
'\u{7A}' === 'z' // true
```
- 允许采用\uxxxx形式表示一个字符
- 超过四位，码点放入大括号，就能正确解读该字符
### 3.2 字符串遍历器接口
```js
let text = String.fromCodePoint(0x20BB7);
for (let i = 0; i < text.length; i++) {
  console.log(text[i]);
}
// " "
// " "
for (let i of text) {
  console.log(i);
}
// "𠮷"
```
- 字符串text只有一个字符，但是for循环会认为它包含两个字符（都不可打印），而for...of循环会正确识别出这一个字符。
- ES6 为字符串添加了遍历器接口，使得字符串可以被for...of循环遍历。
### 3.3 标签模板  

### 3.4 新增方法
**静态方法**
```js
String.fromCodePoint(0x20BB7)
// "𠮷"
String.fromCodePoint(0x78, 0x1f680, 0x79)
// 'x🚀y'

String.raw`Hi\u000A!`;
// 实际返回 "Hi\\u000A!"，显示的是转义后的结果 "Hi\u000A!"
```
- String.fromCodePoint(): 
  - 从Unicode码点返回对应字符 
  - String.fromCharCode()不能识别超过0xFFFF
- String.raw():
  - 返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串
  - 往往用于模板字符串的处理方法。

**实例方法**
```js
let s = '𠮷a';
s.codePointAt(0) // 134071
s.codePointAt(1) // 57271
s.codePointAt(2) // 97

let s = 'Hello world!';
s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false

'x'.repeat(3) // "xxx"

'aabbcc'.replaceAll('b', '_')
// 'aa__cc' 
```
- codePointAt()
  - 可识别超过两个字节的码点
  - 顺序依然不正确，查找a传入2。可用for...of解决
  - 可测试一个字符是两字节还是四字节
- 查找方法 
  - includes()：返回布尔值，表示是否找到了参数字符串。
  - startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
  - endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。
  - 第二个参数从哪一位开始搜索，endsWith(该位向前)
- repeat():返回一个新字符串，表示将原字符串重复n次
- 消除空格 
  - trim()
  - trimStart()
  - trimEnd()
  - 返回一个新字符串，不改变原有
- replaceAll()
  - replace()不使用正则只能匹配第一个
  - 可匹配全部
  - 返回一个新字符串，不改变原有
- at():接受一个整数作为参数，返回参数指定位置的字符，支持负索引
## 正则的扩展