<h2>前言</h2><br>
我现在依然坚信一些js开发者虽然可以熟练的使用js内置函数和对象，也能够封装出出色函数，但是 问题来了，你们在使用非箭头函数的时候this的创建和this判断是不是模糊不清，哈哈我也是，不过我最近总结了一些this的创建和this判断的资料，也包含了自己一些观点，作为分享，文章比较长，请耐心看看，如果有什么不足的地方或者理解错误的地方请留言一下，到时候咱们一起探讨😁。

<h2>目录</h2><br>

*   为什么要用this
*   对this的误解
*   this到底是什么
*   全面解析this

<br><h2>为什么要用this</h2><br>

来直接上代码
```
 function foo(){
     console.log(`我本身属性a是   ${this.a}`)
 }
 
 var bar ={
     a:2,
     foo:foo
 }
 var baz={
     a:4,
     foo:foo
 }
 bar.foo();//我本身属性a是  2
 baz.foo()://我本身属性a是  4
```
小伙伴们是不是已经在这个简单的代码中发现了 刚发foo只定义了一次，去可以被不同的对象引用，实现了代码共享


<br><h2>对this的误解</h2><br>
一般对this的误解分为两个方面

* 1 this是指向当前函数的本身
* 2 this 指向的是当前函数的 作用域

接下来咱们看看代码中的是调试
<h3>this是指向当前函数的本身</h3><br>
下面代码中大家要理解函数的多面性，多个身份

  * 普通的函数
  * 普通的对象
  * 构造函数

接下来讲用到函数的是两个身份普通函数、普通对象， 看代码（）
```
function foo(){
    this.count++
}
var count=0;
foo.count=0;
for(var i=0;i<5;i++){
    
    foo()
}
console.log(foo.count)//0
console.log(count)//5
```
从打印的结果上来看显然，this指向的不是本身函数，当然咱们一般看到这类的问题咱们就会绕道而行，看代码
```
function foo(){
    this.count++
}
var bar={
    count:0
}
foo.count=0;
for(var i=0;i<5;i++){
    
    foo.call(bar)
}
console.log(bar.count)//5
console.log(count)//0
```
虽然这种解决方案很好，也会有其他的解决方案，但是我们还是不理解this的问题，心里还是有种不安之感
<br><h3>this 指向的是当前函数的 作用域</h3><br>
接下来讲用到函数的是两个身份普通函数、普通对象， 看代码（）

```
 function foo(){
     var num=2;
     console.log(this.num)
 }
 var num=0;
 foo()//0
```
咱们看到代码的执行结果后，发现this指向的并不是该函数的作用域。

<br><h2>this到底是什么</h2><br>

this是在函数调用的时候绑定，不是在函数定义的时候绑定。它的上下文取决于函数调用时的各种条件，函数执行的时候会创建一个活动记录，这个记录里面包含了该函数中定义的参数和参数，包含函数在哪里被调用（调用栈）...,this就是其中的一个属性。
来看图
![](https://user-gold-cdn.xitu.io/2019/5/30/16b0801b4f95369d?w=1155&h=398&f=png&s=52948)

图中咱们看到this是在函数执行的时候创建的。


<br><h2>全面解析this</h2><br>

前面几步咱们已经确定的this的创建和this的指向的误区，接下啦咱们要看看this的绑定的规则，分为4个规则。

* 默认绑定
* 隐式绑定（上下文绑定）
* 显式绑定
* new 绑定

<br><h3>默认绑定</h3><br>
默认绑定的字面意思就是，不满足其他的绑定方式，而执行的绑定规则。默认绑定会把this绑定到全局对象（是一个危险的操作，文章后面会说为什么）
看代码
```
 function foo(){
     var num=2;
     this.num++
     console.log(this.num)
 }
 var num=0;
 foo()//1
```

上面代码中就实现了默认绑定，在foo方法的代码块中操作的是window.num++。

<br><h3>隐式绑定（上下文绑定）</h3><br>
定义：<br>
函数被调用的位置有上下文，或者是该函数的引用地址是不是被某个对象的属性引用，并通过对象的属性直接运行该函数。如果出现上述的情况，就会触发this的隐式绑定，this就会被绑定成当前对象
看代码
```
function foo(){
    console.log(this.name)
}
var bar={
    name:'shiny',
    foo:foo
}
bar.foo()//shiny
```

![](https://user-gold-cdn.xitu.io/2019/5/30/16b0812c7acfc86e?w=895&h=245&f=png&s=37387)

要需要补充一点，不管你的对象嵌套多深，this只会绑定为直接引用该函数的地址属性的对象，看代码
```
function foo(){
    console.log(this.name)
}
var shiny={
    name:'shiny',
    foo:foo
}
var red={
    name:'red',
    obj:shiny
    
}
red.obj.foo()//shiny
```

![](https://user-gold-cdn.xitu.io/2019/5/30/16b0819d7d3d3c30?w=1156&h=477&f=png&s=66256)

<br><h4>隐式绑定的丢失</h4><br>
先看代码
```
function foo(){
    console.log(this.name)
}
var shiny={
    name:'shiny',
    foo:foo
}
function doFoo(fn){
    fn()
}
doFoo(shiny.foo)//undefind
```
大家知道函数参数在函数执行的时候，其实有一个赋值的操作，我来解释一下上面的，当函数doFoo执行的时候会开辟一个新的栈并被推入到全局栈中执行，在执行的过程中会创建一个活动对象，这个活动对象会被赋值传入的参数以及在函数中定义的变量函数，在函数执行时用到的变量和函数直接从该活动对象上面取值使用。
看图
doFoo的执行栈

![](https://user-gold-cdn.xitu.io/2019/5/30/16b0827875e62581?w=1099&h=495&f=png&s=82657)

fn的执行栈

![](https://user-gold-cdn.xitu.io/2019/5/30/16b0829133116157?w=1188&h=501&f=png&s=75812)

看下面原理和上面一样通过赋值，导致隐式绑定的丢失，看代码
```
function foo(){
    console.log(this.name)
}
var shiny={
    name:'shiny',
    foo:foo
}
var bar = shiny.foo
bar()//undefined
```
大家是不是已经明白了为什么是undefined，来解释一波，其实shiny的foo属性是引用了foo函数的引用内存地址，那么有把foo的引用地址赋值给了 bar 那么现在的bar的引用地址个shiny.foo的引用地址是一个，那么执行bar的时候也会触发默认绑定规则因为没有其他规则可以匹配，bar函数执行时，函数内部的this绑定的是全局变量。

![](https://user-gold-cdn.xitu.io/2019/5/30/16b0838cbec832bb?w=933&h=338&f=png&s=62662)

看下满的引用地址赋值是出现的，奇葩 隐式绑定丢失，看代码
```
function foo(){
    console.log(this.name)
}
var shiny={
    name:'shiny',
    foo:foo
}
var red={
    name:'red'
}
(red.foo=shiny.foo)()//undefined
```
赋值表达式 p.foo = o.foo 的返回值是目标函数的引用，因此调用位置是 foo() 而不是
p.foo() 或者 o.foo()。根据我们之前说过的，这里会应用默认绑定。

![](https://user-gold-cdn.xitu.io/2019/5/31/16b0be399b8f06a6?w=808&h=287&f=png&s=31984)

<br><h3>显式绑定</h3><br>

<h4>call、apply绑定</h4><br>
javascript,在Function的porpertype上提供了3个方法来强行修改this，分别是 call、apply、bind，大家经常用的莫过于call和apply了，这两个函数的第一个参数，都是需要执行函数绑定的this，对于apply只有连个参数，第二个参数是一个数组，这个数组是要传入执行函数的参数，而call可以跟很多参数，从第二个参数起都会被传入到要执行函数的参数中

看代码
```
function foo(){
   console.log(this.age)
}
var shiny={
   age:20
}
foo.call(shiny)//20

function bar(){
console.log(this.age)
}
var red={
age:18
}
bar.apply(red)//18
```
这两个方法都是显式的绑定了tihs
<br><h4>硬绑定：</h4><br>
类似与 bind方法行为，是显式绑定的一种方式
```
function foo(b){
  return this.a+b
}
var obj={
  a:2
}
function bind(fn,obj){
  return function(){
     return fn.apply(obj,arguments)
  }
}
bind(foo,obj)(3)//5
```
语言解释：
通过apply + 闭包机制 实现bind方法，实现强行绑定规则


API调用的“上下文”
第三方库或者寄生在环境，以及js内置的一些方法都提供了一下 content 上下文参数，他的作用和 bind一样，就是确保回调函数的this被绑定
```
function foo (el){
  console.log(el,this.id)
}
var obj ={
 id:'some one'
};
[1,2,4].forEach(foo,obj)
// 1 some one 2 some one 4 some one
```


<br><h3>new 绑定</h3><br>
说道new 大家都会想到js的构造函数，咱们想不用着急new 绑定this的问题，咱们先看看咱们对js的构造函数的误解，传统面向类的语言中的构函数和js的构造函数时不一样

* 传统面向类的语言中的构函数，是在使用new操作符实例化类的时候，会调用类中的一些特殊方法（构造函数）
* 很多人认为js中的new操作符和传统面向类语言的构造函数是一样的，其实有很大的差别
* 从新认识一下js中的构造函数，js中的构造函数 在被new操作符调用时，这个构造函数不属于每个类，也不会创造一个类，它就是一个函数，只是被new操作符调用。
* 使用new操作符调用 构造函数时会执行4步
   
   * 创建一个全新的对象
   * 对全新的对象的__proto__属性地址进行修改成构造函数的原型（prototype）的引用地址
   * 构造函数的this被绑定为这个全新的对象
   * 如果构造函数有返回值并且这个返回值是一个对象，则返回该对象，否则返回当前新对象
   

咱们了解了js new 操作符调用构造函数时都做了些什么，哪么咱们就知道构造函数里面的this是谁了

代码实现
```
function Foo(a){
  this.a=a
}
var F = new Foo(2)
console.log(F.a)//2
```

<br><h3>绑定规则的顺序 </h3><br>
咱们在上面了解this绑定的4大规则，那么咱们就看看这4大绑定规则的优先级。
<br><h4>默认绑定 </h4>
咱们根据字面意思，都能理解只有其余的3个绑定规则无法触发的时候就会触发默认绑定，没有比较意义

<br><h4>显式绑定 VS 隐式绑定</h4><br>

看代码
```
function foo(){
    console.log(this.name)
}
var  shiny={
    name:'shiny',
    foo:foo
}
var red={
    name:'red'
}

shiny.foo()//shiny
shiny.foo.call(red)// red
shiny.foo.apply(red)// red
shiny.foo.bind(red)()//red
```
显然在这场绑定this比赛中，显式绑定赢了隐式绑定

<br><h4>隐式绑定 VS new 操作符绑定</h4><br>
看代码
```
function  foo(name){
    this.name=name
}
var shiny={
    foo:foo
}
shiny.foo('shiny')
console.log(shiny.name)//shiny

var red = new shiny.foo('red')
console.log(red.name)//red
```

显然在这场绑定this比赛中new 操作符绑定赢了隐式绑定

<br><h4>显式绑定（硬绑定） VS new 操作符绑定</h4><br>

使用call、apply方法不能结合new操作符会报错误

![](https://user-gold-cdn.xitu.io/2019/5/31/16b0cae7714a145a?w=545&h=118&f=png&s=19364)
但是咱们可以是bind绑定this来比较 显式绑定和new操作符的绑定this优先级。
看代码
```
function foo(){
    console.log(this.name)
}
var shiny={
    name:'shiny'
}

var bar = foo.bind(shiny)
var obj = new bar();
console.log(obj.name)// undefind
```

显然 new操作符绑定 战胜了 显式绑定

<br><h4>this的判断</h4><br>
咱们在上面已经了解 4个绑定this的优先级。咱们可以列举出来

* 1 判断该函数是不是被new操作符调用，有的话 this就是 构造函数运行时创建的新对象
      var f = new foo()
* 2 判断 函数是不是使用显式绑定 call、apply、bind，如果有，那么该函数的this就是 这个三个方法的第一个参数
foo.call(window)
* 3 判断该函数是不是被一个对象的属性引用了地址，该函数有上下文（隐式绑定），在函数执行的时候是通过该对象属性的引用触发，这个函数的this就是当前对象的。
obj.foo();
* 4 上面的三种都没有的话，就是默认绑定，该函数的this就是全局对象或undefined（严格模式下）


<br><h4>绑定例外</h4><br>
😁 规则总是会有意外的，this绑定也是会有的，某些场面的绑定也是会出乎意料的，有可能触发了默认绑定
看代码
```
function foo(){
    console.log(name)
}
var name ='shiny'
foo.call(null)//shiny
foo.call(undefined)//shiny
var bar = foo.bind(null)
var baz = foo.bind(undefined)
bar()//siny
baz()//siny
```
把 null、undefined通过 apply、call、bind 显式绑定，虽然实现可默认绑定，但是建议这么做因为在非严格的模式下会给全局对象添加属性，有时候会造成不可必要的bug。

<br><h4>更安全的this</h4><br>
咱们从上面知道在非严格模式下 默认绑定是并操作this的话会该全局对象添加属性，这样的操作是有风险性的
```
function foo(a,b) {
console.log( "a:" + a + ", b:" + b );
}
// 我们的空对象
var ø = Object.create( null );
// 把数组展开成参数
foo.apply( ø, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind( ø, 2 );
bar( 3 ); // a:2, b:3

```
<br><h4>es6中的this</h4><br>
在es5及一下版本，我们被this深深的困惑，但是看完了上面的文章，应该判断this没有关系，但是 重点来了 es6的this可以通过箭头函数直接绑定在该函数的执行的作用域上。
看代码
```
 function foo(){
     return ()=>{
          console.log(this.name)
     }
 }
 var obj ={
     name:'obj'
 }
  var shiny ={
     name:'shiny'
 }
 var bar = foo.call(obj);
 bar.call(shiny)// foo
 

```
我们看到箭头函数的this被绑定到该函数执行的作用域上。

咱们在看看 js内部提供内置函数使用箭头函数
```
 function foo() {
    setTimeout(() => {
    // 这里的 this 在此法上继承自 foo()
    console.log( this.a );
    },100);
}
var obj = {
    a:2
};
foo.call( obj ); // 2
```

![](https://user-gold-cdn.xitu.io/2019/5/31/16b0d03081f5ab05?w=1155&h=435&f=png&s=70688)
箭头函数可以像 bind(..) 一样确保函数的 this 被绑定到指定对象，此外，其重要性还体
现在它用更常见的词法作用域取代了传统的 this 机制。实际上，在 ES6 之前我们就已经
在使用一种几乎和箭头函数完全一样的模式。
```
function foo() {
var self = this; // lexical capture of this
setTimeout( function(){
    console.log( self.a );
    }, 100 );
}
var obj = {
    a: 2
};
foo.call( obj ); // 2
```
虽然 self = this 和箭头函数看起来都可以取代 bind(..)，但是从本质上来说，它们想替
代的是 this 机制。
如果你经常编写 this 风格的代码，但是绝大部分时候都会使用 self = this 或者箭头函数。
如果完全采用 this 风格，在必要时使用 bind(..)，尽量避免使用 self = this 和箭头函数。

**如有不足，在评论中提出，咱们一起学习**