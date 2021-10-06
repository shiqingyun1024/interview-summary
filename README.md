# interview-summary
关于面试题的一些总结
## 1、JavaScript基础（重点）
### 1.1 执行上下文/作用域链/闭包/垃圾回收机制
#### 1.1.1 执行上下文
![Image text](https://raw.githubusercontent.com/hongmaju/light7Local/master/img/productShow/20170518152848.png)
```
什么是执行上下文？
简而言之，执行上下文是评估和执行 JavaScript 代码的环境的抽象概念。每当 Javascript 代码在运行的时候，它都是在执行上下文中运行。 --- https://juejin.cn/post/6844903682283143181

执行上下文的创建阶段，会分别生成变量对象，建立作用域链，确定this指向。https://www.jianshu.com/p/d647aa6d1ae6

https://www.cnblogs.com/echolun/p/11438363.html
1、执行上下文
JS代码在执行前，JS引擎总要做一番准备工作，这份工作其实就是创建对应的执行上下文；
执行上下文有且只有三类，全局执行上下文，函数上下文，与eval上下文；由于eval一般不会使用，这里不做讨论。
1.1、全局执行上下文
全局执行上下文只有一个，在客户端中一般由浏览器创建，也就是我们熟知的window对象，我们能通过this直接访问到它。
1.2、函数执行上下文
函数执行上下文可存在无数个，每当一个函数被调用时都会创建一个函数上下文；需要注意的是，同一个函数被多次调用，都会创建一个新的上下文。
说到这你是否会想，上下文种类不同，而且创建的数量还这么多，它们之间的关系是怎么样的，又是谁来管理这些上下文呢，这就不得不说说执行上下文栈了。

执行上下文栈(执行栈)
执行上下文栈(下文简称执行栈)也叫调用栈，执行栈用于存储代码执行期间创建的所有上下文，具有LIFO（Last In First Out后进先出，也就是先进后出）的特性。

JS代码首次运行，都会先创建一个全局执行上下文并压入到执行栈中，之后每当有函数被调用，都会创建一个新的函数执行上下文并压入栈内；由于执行栈LIFO的特性，所以可以理解为，JS代码执行完毕前在执行栈底部永远有个全局执行上下文。

**注意：变量环境可以说也是词法环境，它具备词法环境所有属性，一样有环境记录与外部环境引入。在ES6中唯一的区别在于词法环境用于存储函数声明与let const声明的变量，而变量环境仅仅存储var声明的变量。**

总结：
1.全局执行上下文一般由浏览器创建，代码执行时就会创建；函数执行上下文只有函数被调用时才会创建，调用多少次函数就会创建多少上下文。

2.调用栈用于存放所有执行上下文，满足FILO规则。

3.执行上下文创建阶段分为绑定this，创建词法环境，变量环境三步，两者区别在于词法环境存放函数声明与const let声明的变量，而变量环境只存储var声明的变量。

4.词法环境主要由环境记录与外部环境引入记录两个部分组成，全局上下文与函数上下文的外部环境引入记录不一样，全局为null，函数为全局环境或者其它函数环境。环境记录也不一样，全局叫对象环境记录，函数叫声明性环境记录。

5.你应该明白了为什么会存在变量提升，函数提升，而let const没有。

6.ES3之前的变量对象与活动对象的概念在ES5之后由词法环境，变量环境来解释，两者概念不冲突，后者理解更为通俗易懂。

变量环境--var 变V
词法环境--函数声明与const let 词函
```

#### 1.1.2、作用域链
```
https://github.com/mqyqingfeng/Blog/issues/6
什么是作用域链？
当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。
在《JavaScript深入之词法作用域和动态作用域》中讲到，函数的作用域在函数定义的时候就决定了。

这是因为函数有一个内部属性 [[scope]]，当函数创建的时候，就会保存所有父变量对象到其中，你可以理解 [[scope]] 就是所有父变量对象的层级链，但是注意：[[scope]] 并不代表完整的作用域链！
```
#### 1.1.3、闭包和垃圾回收机制
```
https://blog.csdn.net/dovlie/article/details/76339244
什么是闭包？
一个函数和它的周围状态的引用捆绑在一起的组合
我们都知道，js的作用域分两种，全局和局部，基于我们所熟悉的作用域链相关知识，我们知道在js作用域环境中访问变量的权利是由内向外的，内部作用域可以获得当前作用域下的变量并且可以获得当前包含当前作用域的外层作用域下的变量，反之则不能，也就是说在外层作用域下无法获取内层作用域下的变量，同样在不同的函数作用域中也是不能相互访问彼此变量的，那么我们想在一个函数内部也有限权访问另一个函数内部的变量该怎么办呢？闭包就是用来解决这一需求的，闭包的本质就是在一个函数内部创建另一个函数。

应用场景？
1、在一个函数中作为参数传入另外一个函数
例如回调函数，有时候我们希望对异步请求的数据结果进行操作，例如对一个异步返回的数组，求最大值
function foo (callback) {
  new Promise((resolve) => {
    resolve([1, 2, 3, 4])
  }).then(res => {
    callback(res)
  })
}
 
function bar (arr) {
  arr.push(10) // arr [1, 2, 3, 4, 10 ]
  arr.push(6) // arr [ 1, 2, 3, 4, 10, 6 ]
  Math.max.apply(null, arr) // 10 求最大值
}
 
foo(bar) 
// 这就形成了一个闭包，完全符合闭包的定义

2、实现bind函数：其作用是改变this的指向
// getName 的 this指向foo对象
let foo = {
  name: 'jill'
}
function getName () {
  console.log(this.name)
}
Function.prototype.myBind = function (obj) {
  // 将当前函数的this 指向目标对象
  let _self = this
  return function () {
    return _self.call(obj)
  }
}
 
let getFooName = getName.myBind(foo)
getFooName() // jill

3、异步循环调用
for (var i = 0; i < 2; i++) {
  (function (i) {
    setTimeout(() => {
      console.log(i)
    })
  })(i)
}

闭包的优缺点？
闭包有三个特性：
1、函数嵌套函数。
2、函数内部可以引用外部的参数和变量。
3、参数和变量不会被垃圾回收机制回收。

闭包的好处：
1、希望一个变量长期存储在内存中。
2、避免全局变量的污染。
3、私有成员的存在。

闭包的缺点：
1、常驻内存，增加内存使用量。
2、使用不当会很容易造成内存泄露。

那怎么释放内存呢？
以下面为例
直接写一个闭包函数：
  var f = function() {
    var num = 0;
    return function() {
      return num += 1;
    };
  }
  js中，函数是一等公民，定义一个函数f，它返回另一个可执行函数function() { return num += 1; };
  js中的作用域，都是一层一层向上找的，在f内部函数里面，他的num向上找到父函数的作用域。

现在，我们执行一下：
f()()
// 1
f()()
// 1
????? 感觉 不太对？为什么num没有自增呢？？

内存回收机制
为什么上面执行结果不太对，因为执行f()()后，f函数已经执行完毕了，没有其他资源引用f，ta会被立即释放，也就是说，f()()执行完后，立即就释放了。
如何才不释放呢？

// 创建f的引用
var fn = f();
fn()
// 1
fn()
// 2
fn()
// 3

这下就对了，num成了私有变量，f拥有了私有作用域。
完了吗？
f有了fn的引用，内存一直得不到释放，咋办呢？这样的函数多了是不是会造成内存溢出？
手动释放一下：

var fn = f();
fn()
// 1
fn()
// 2
fn()
// 3
// 手动释放f的引用
fn = null
// f的引用fn被释放了，现在f的作用域也被释放了。num再次归零了。
var fn = f()
fn()
// 1

**注意：最后的结论是手动释放内存，让引用的值直接赋值为null。**

```
#### 1.1.4、垃圾回收机制
```
由于字符串、对象和数组没有固定大小，当他们的大小已知时，才能对他们进行动态的存储分配。JavaScript程序每次创建字符串、数组或对象时，解释器都必须分配内存来存储那个实体。只要像这样动态地分配了内存，最终都要释放这些内存以便他们能够被再用，否则，JavaScript的解释器将会消耗完系统中所有可用的内存，造成系统崩溃。

现在各大浏览器通常用采用的垃圾回收有两种方法：标记清除、引用计数。

标记清除
这是javascript中最常用的垃圾回收方式。当变量进入执行环境是，就标记这个变量为“进入环境”。从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入相应的环境，就可能会用到他们。当变量离开环境时，则将其标记为“离开环境”。

引用计数
　另一种不太常见的垃圾回收策略是引用计数。引用计数的含义是跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型赋值给该变量时，则这个值的引用次数就是1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数就减1。当这个引用次数变成0时，则说明没有办法再访问这个值了，因而就可以将其所占的内存空间给收回来。这样，垃圾收集器下次再运行时，它就会释放那些引用次数为0的值所占的内存。
```

### 1.2 this/call/apply/bind
#### 1.2.1、this
```
执行上下文的创建阶段，会分别生成变量对象，建立作用域链，确定this指向。其中变量对象与作用域链我们都已经明白了。本文的关键，就是确定this指向。
首先，我们需要得出一个非常重要的，并且一定要牢记于心的结论，this的指向，是在函数被调用的时候确定的。也就是执行上下文被创建时确定的。https://www.jianshu.com/p/d647aa6d1ae6
可以这样理解，在JavaScript中，this的指向是调用时决定的，而不是创建时决定的，这就会导致this的指向会让人迷惑，简单来说，this具有运行期绑定的特性。https://github.com/axuebin/articles/issues/6

```
#### 1.2.2、call
```
        // 日拱一卒。
        // 手写call的实现原理，一定挂载在Function.prototype上，这样所有的函数或者方法才能使用，原型链还需要再深入的了解一下。
        Function.prototype.CopyCall = function(context){
            // 如果调用CopyCall的不是一个函数，直接返回，因为下面要调用这个函数，如果不是函数，就没法调用。
            if(typeof this !== 'function') return;
            // 考虑到context是null的情况
            context = context || window
            // arguments不能直接使用slice，因为它只是一个类数组，不能使用slice
            let params = [...arguments].slice(1)
            // 把要调用的方法赋值到context.fn，使用完之后再删除。
            context.fn = this
            // 这一步的时候一定要用扩展运算符，因为params是一个数组。
            let result = context.fn(...params);
            // 删除context.fn这个属性值
            delete context.fn;
            // 返回执行后的结果。
            return result
        }
        let maxNumber = Math.max.CopyCall(null,1,2,3,4);
        console.log(maxNumber);
        // 手写关键的点是方法加载Function.prototype上，这样所有的函数都可以使用了。
        // context = context || window，考虑到context是null的情况下。
        // 对于参数的处理，arguments是一个类数组，所以处理时要先转化为数组，然后拿到对应的参数。
```
#### 1.2.3、apply
```
         // 日拱一卒。
        // 手写call的实现原理，一定挂载在Function.prototype上，这样所有的函数或者方法才能使用，原型链还需要再深入的了解一下。
        Function.prototype.CopyApply = function(context){
            // 如果调用CopyCall的不是一个函数，直接返回，因为下面要调用这个函数，如果不是函数，就没法调用。
            if(typeof this !== 'function') return;
            // 考虑到context是null的情况
            context = context || window
            // arguments不能直接使用slice，因为它只是一个类数组，不能使用slice
            let params = [...arguments].slice(1)
            // 把要调用的方法赋值到context.fn，使用完之后再删除。
            context.fn = this
            // 这一步的时候一定要用扩展运算符，因为params是一个数组。
            let result = context.fn(params);
            // 删除context.fn这个属性值
            delete context.fn;
            // 返回执行后的结果。
            return result
        }
        let maxNumber = Math.max.CopyApply(null,[1,2,3,4]);
        console.log(maxNumber);
        // 手写关键的点是方法加载Function.prototype上，这样所有的函数都可以使用了。
        // context = context || window，考虑到context是null的情况下。
        // 对于参数的处理，arguments是一个类数组，所以处理时要先转化为数组，然后拿到对应的参数。
```
#### 1.2.4、bind
```
          Function.prototype.myBind = function () {
            if (typeof this !== 'function') throw 'caller must be a function'
            let self = this // 这里是关键 用来和new出来的比较原型来判断是否为new出来的  当前函数对象
            let context = arguments[0] || {}
            let args = Array.prototype.slice.call(arguments, 1) // 旧：参数
            let fn = function () {
                let fnArgs = Array.prototype.slice.call(arguments) // 新：参数
                // bind 函数的参数 + 延迟函数的参数
                // 用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上
                self.apply(this instanceof self ? this : context, args.concat(fnArgs))
            }
            fn.prototype = Object.create(self.prototype) // 维护原型
            return fn
        }
```
### 1.3 原型/继承
#### 1.3.1 原型
```
https://blog.csdn.net/xiaolinlife/article/details/119291940


```
### 1.3.2 继承
```
《JavaScript高级程序设计》提到了6中继承方式：
1.原型链继承
2.借用构造函数（经典继承）
3.组合继承
4.原型式继承
5.寄生式继承
6.寄生组合式继承
```
#### 1.3.2.1 原型链继承
```
        // 原型链继承
        function Person(){
            this.name = 'xiaopao';
        }

        Person.prototype.getName = function(){
            console.log(this.name);
        }

        function Child(){
            
        }

        Child.prototype = new Person();  // 这一步是最关键的。
        var child1 = new Child();
        child1.getName(); // xiaopao

    缺点：

    1、引用类型的属性（也就是Person的值）被所有实例共享
    2、在创建Child 的实例时， 不能向Person传参

        function Person(){
            this.name = 'xiaopao';
            this.colors = ['red', 'blue', 'green'];
        }

        Person.prototype.getName = function(){
            console.log(this.name);
        }

        function Child(){

        }

        Child.prototype = new Person(); // 这一步是最关键的，Child的原型指向Person的实例。Child的实例，就可以使用Person实例上的方法和属性和实例原型上的方法和属性。
        var child1 = new Child();
        var child2 = new Child();
        child1.colors.push('yellow');
        console.log(child1.colors);
        console.log(child2.colors);
```  
#### 1.3.2.2、借用构造函数（经典继承）
```
复制父类构造函数内的属性

        // 借用构造函数继承（经典继承）
        function Person(){
            this.name = 'xiaopao';
            this.colors = ['red', 'blue', 'green'];
        }

        Person.prototype.getName = function(){
            console.log(this.name);
        }

        function Child(){
            Person.call(this);// 这一步是最关键的，直接改变了Person的this指向。this的值是变动的，new出来的实例中的name和colors都是唯一的值。
        }

        var child1 = new Child();
        var child2 = new Child();
        child1.colors.push('yellow');
        console.log(child1.name);
        console.log(child1.colors); // ["red", "blue", "green", "yellow"]
        console.log(child2.colors); // ["red", "blue", "green"]

优点：
1.避免了引用类型的属性被所有实例共享
2.可以在Child中向Parent传参

缺点：
1.只是子类的实例，不是父类的实例
2.方法都在构造函数中定义，每次创建实例都会创建一遍方法

       // 借用构造函数继承， 向Parent传参
       function Person(name){
            this.name = name;
        }

        Person.prototype.getName = function(){
            console.log(this.name);
        }

        function Child(name){
            Person.call(this,name); // 这一步是最关键的。
        }

        var child1 = new Child('xiaopao');
        var child2 = new Child('lulu');
        console.log(child1.name); // xiaopao
        console.log(child2.name); // lulu
        console.log(child1 instanceof Person); // false   不能识别是Person的实例

 **注意：function Child(name){
            Person.call(this,name); // 这一步是最关键的。
        }**       
```
#### 1.3.2.3、组合继承
组合 原型链继承 和 借用构造函数继承
背后的思路是：使用原型链实现对原型方法的继承，而通过借用构造函数来实现对实例属性的继承。
```
        function Parent(name){
            this.name = name;
            this.colors = ['red', 'blue', 'green'];
        }

        Parent.prototype.getName = function(){
            console.log(this.name);
        }

        function Child(name,age){
            Parent.call(this,name);// 第二次调用 Parent()  这一步是 借用构造函数
            this.age = age;
        }

        Child.prototype = new Parent(); // 第一次调用 Parent()  这一步是 原型链继承

        var child1 = new Child('xiaopao',18);
        var child2 = new Child('lulu',19);
        child1.getName(); // xiaopao
        child2.getName(); // lulu
        console.log(child1.age); // 18
        console.log(child2.age); // 19
        child1.colors.push('yellow');
        console.log(child1.colors);  // ["red", "blue", "green", "yellow"]
        console.log(child2.colors); // ["red", "blue", "green"]
        console.log(child1 instanceof Child); // true
        console.log(child1 instanceof Parent); // true

优点：融合原型链继承和构造函数的优点，是JavaScript中最常用的继承模式
缺点：调用了两次父类构造函数
（组合继承最大的问题是无论什么情况下，都会调用两次超类型构造函数：一次是在创建子类型原型的时候，另一次是在子类型构造函数内部）
```
#### 1.3.2.4、原型式继承
```
        es6中的原型式继承新的写法就是Object.create()
        // 原型式继承  
        function CreateObj(o){
            function F(){}  // 主要的步骤
            F.prototype = o;  // 主要的步骤
            console.log(o.__proto__ === Object.prototype);
            console.log(F.prototype.constructor === Object); // true
            return new F();  // 主要的步骤
        }

        var person = {
            name: 'xiaopao',
            friend: ['daisy','kelly']
        }

        var person1 = CreateObj(person);  // 主要的步骤
        // var person2 = CreateObj(person);

        person1.name = 'person1';
        // console.log(person2.name); // xiaopao
        person1.friend.push('taylor');
        // console.log(person2.friend); // ["daisy", "kelly", "taylor"]
        // console.log(person); // {name: "xiaopao", friend: Array(3)}
        person1.friend = ['lulu'];
        // console.log(person1.friend); // ["lulu"]
        // console.log(person.friend); //  ["daisy", "kelly", "taylor"]
        // 注意： 这里修改了person1.name的值，person2.name的值并未改变，并不是因为person1和person2有独立的name值，而是person1.name='person1'是给person1添加了name值，并非修改了原型上的name值
        // 因为我们找对象上的属性时，总是先找实例上对象，没有找到的话再去原型对象上的属性。实例对象和原型对象上如果有同名属性，总是先取实例对象上的值

缺点： 包含引用类型的属性值始终都会共享相应的值， 这点跟原型链继承一样
注意： 这里修改了person1.name的值，person2.name的值并未改变，并不是因为person1和person2有独立的name值，而是person1.name='person1'是给person1添加了name值，并非修改了原型上的name值。
因为我们找对象上的属性时，总是先找实例上对象，没有找到的话再去原型对象上的属性。实例对象和原型对象上如果有同名属性，总是先取实例对象上的值        
```
     
#### 1.3.2.5、寄生式继承
创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。
可以理解为在原型式继承的基础上新增一些函数或属性
```
        // 寄生式继承  可以理解为在原型式继承的基础上增加一些函数或属性
        var ob = {
            name: 'xiaopao',
            friends: ['lulu','huahua']
        }

        function CreateObj(o){
            function F(){};  // 创建一个构造函数F
            F.prototype = o;
            return new F();
        }

        // 上面CreateObj函数 在ECMAScript5 有了一新的规范写法，Object.create(ob) 效果是一样的 , 看下面代码
        var ob1 = CreateObj(ob);
        var ob2 = Object.create(ob);
        console.log(ob1.name); // xiaopao
        console.log(ob2.name); // xiaopao

        function CreateOb(o){
            var newob = CreateObj(o); // 创建对象 或者用 var newob = Object.create(ob)
            newob.sayName = function(){ // 增强对象
                console.log(this.name);
            }
            return newob; // 指定对象
        }

        var p1 = CreateOb(ob);
        p1.sayName(); // xiaopao 
```


#### 1.3.2.6、寄生组合式继承
```
子类构造函数复制父类的自身属性和方法，子类原型只接收父类的原型属性和方法

所谓寄生组合继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。
其背后的基本思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型的原型的一个副本而已。本质上，就是使用寄生式继承来继承超类型的原型，然后再将结果指定给予类型的原型。

        // 寄生组合式继承
        function Parent(name){
            this.name = name;
            this.colors = ['red', 'blue', 'green'];
        }

        Parent.prototype.sayName = function(){
            console.log(this.name);
        }

        function Child(name,age){
            Parent.call(this,name); 
            this.age = age;
        }

        function CreateObj(o){
            function F(){};
            F.prototype = o;
            return new F();
        }

        // Child.prototype = new Parent(); // 这里换成下面
        <!-- 最关键的函数实现 -->
        function prototype(child,parent){
            var prototype = CreateObj(parent.prototype);   // var prototype = Object.create(parent.prototype)
            prototype.constructor = child;
            child.prototype = prototype;
        }
        prototype(Child,Parent);

        var child1 = new Child('xiaopao', 18);
        console.log(child1); 
```

## 2、CSS基础（重点）
### 2.1 position
```
position的属性有哪些？
分别的作用是？
https://blog.csdn.net/xiaolinlife/article/details/104872893
```
### 2.2 行内元素/块级元素
```
块级元素和行内元素的分类：
　　在HTML的角度来讲，标签分为：

　　　　文本级标签：p , span , a , b , i , u , em

　　　　容器级标签：div , h系列 , li , dt ,dd

　　　　p：里面只能放文字和图片和表单元素，p里面不能放h和ul，也不能放p。

　　从CSS的角度讲，CSS的分类和上面的很像，就p不一样：

　　　　行内元素：除了p之外，所有的文本级标签，都是行内元素。p是个文本级标签，但是是个块级元素。

　　　　块级元素：所有的容器级标签，都是块级元素，以及p标签。
```
### flex
```
已总结
思维导图和博客
http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html

flex:1的解释
https://www.cnblogs.com/LangZ-/p/12703858.html
flex属性 是 flex-grow、flex-shrink、flex-basis三个属性的缩写。

推荐使用此简写属性，而不是单独写这三个属性。

flex-grow：定义项目的的放大比例；

        默认为0，即 即使存在剩余空间，也不会放大；
       所有项目的flex-grow为1：等分剩余空间（自动放大占位）；
        flex-grow为n的项目，占据的空间（放大的比例）是flex-grow为1的n倍。
        

flex-shrink：定义项目的缩小比例；

         默认为1，即 如果空间不足，该项目将缩小；
         所有项目的flex-shrink为1：当空间不足时，缩小的比例相同；
         flex-shrink为0：空间不足时，该项目不会缩小；
         flex-shrink为n的项目，空间不足时缩小的比例是flex-shrink为1的n倍。
 

flex-basis： 定义在分配多余空间之前，项目占据的主轴空间（main size），浏览器根据此属性计算主轴是否有多余空间，

         默认值为auto，即 项目原本大小；
         设置后项目将占据固定空间。
         

   所以flex属性的默认值为：0 1 auto （不放大会缩小）

   flex为none：0 0 auto  （不放大也不缩小）

   flex为auto：1 1 auto  （放大且缩小）

   

   flex为一个非负数字n：该数字为flex-grow的值，

   flex：n；=  flex-grow：n；

                     flex-shrink：1；

                     flex-basis：0%；

           

   flex为两个非负数字n1，n2： 分别为flex-grow和flex-shrink的值，

   flex：n1 n2; = flex-grow：n1；

                           flex-shrink：n2；

                           flex-basis：0%；

             

   flex为一个长度或百分比L：视为flex-basis的值，

   flex: L; =  flex-grow：1；

                   flex-shrink：1；

                   flex-basis：L；

           

   flex为一个非负数字n和一个长度或百分比L：分别为flex-grow和flex-basis的值，

   flex：n L；= flex-grow：n；

                        flex-shrink：1；

                        flex-basis：L;

            

   可以发现，flex-grow和flex-shrink在flex属性中不规定值则为1，flex-basis为0%。

   到这里我已经解决我的问题啦，flex：1即为flex-grow：1，经常用作自适应布局，将父容器的display：flex，侧边栏大小固定后，将内容区flex：1，内容区则会自动放大占满剩余空间。
```

## 3、框架(Vue为主 重点)
### MVVM 
#### 介绍一下MVVM，和MVC有什么区别
```
MVC（Model-View-Controller）
MVC的概念：
MVC把GUI分成View（视图）、Model（模型）、Controller（控制
器）（可热插拔，主要进行Model和View之间的协作，包括路由、输入预处理等业务逻辑）三个模块：

View：检测用户的键盘、鼠标等行为，传递调用Controller执行应用逻辑。View更新需要重新获取Model的数据。
Controller：View和Model之间协作的应用逻辑或业务逻辑处理。
Model：Model变更后，通过观察者模式通知View更新视图。
Model的更新通过观察者模式，可以实现多视图共享同一个Model。
传统的MVC设计对于Web前端开发而言是一种十分有利的模式，因为View是持续性的，并且View可以对应不同的Model。Backbone.js就是一种稍微变种的MVC模式实现（和经典MVC较大的区别在于View可以直接操作Model，因此这个模式不能同构）。这里总结一下MVC设计模式可能带来的好处以及不够完美的地方：
优点：
职责分离：模块化程度高、Controller可替换、可复用性、可扩展性强。
多视图更新：使用观察者模式可以做到单Model通知多视图实现数据更新。

缺点：
测试困难：View需要UI环境，因此依赖View的Controller测试相对比较困难（现在Web前端的很多测试框架都已经解决了该问题）。
依赖强烈：View强依赖Model(特定业务场景)，因此View无法组件化设计。

MVP（Model-View-Presenter)
MVP是MVC的模式的一种改良，打破了View对于Model的依赖，其余的依赖关系和MVC保持不变。

Passive View：View不再处理同步逻辑，对Presenter提供接口调用。由于不再依赖Model，可以让View从特定的业务场景中抽离，完全可以做到组件化。
Presenter（Supervising Controller）：和经典MVC的Controller相比，任务更加繁重，不仅要处理应用业务逻辑，还要处理同步逻辑(高层次复杂的UI操作)。
Model：Model变更后，通过观察者模式通知Presenter，如果有视图更新，Presenter又可能调用View的接口更新视图。

MVP模式可能产生的优缺点如下：

Presenter便于测试、View可组件化设计
Presenter厚、维护困难

MVVM（Model-View-ViewModel)
MVVM模式是在MVP模式的基础上进行了改良，将Presenter改良成ViewModel（抽象视图）：

ViewModel：内部集成了Binder(Data-binding Engine，数据绑定引擎)，在MVP中派发器View或Model的更新都需要通过Presenter手动设置，而Binder则会实现View和Model的双向绑定，从而实现View或Model的自动更新。
View：可组件化，例如目前各种流行的UI组件框架，View的变化会通过Binder自动更新相应的Model。
Model：Model的变化会被Binder监听(仍然是通过观察者模式)，一旦监听到变化，Binder就会自动实现视图的更新。
可以发现，MVVM在MVP的基础上带来了大量的好处，例如：

提升了可维护性，解决了MVP大量的手动同步的问题，提供双向绑定机制。
简化了测试，同步逻辑是交由Binder处理，View跟着Model同时变更，所以只需要保证Model的正确性，View就正确。
当然也带来了一些额外的问题：

产生性能问题，对于简单的应用会造成额外的性能消耗。
对于复杂的应用，视图状态较多，视图状态的维护成本增加，ViewModel构建和维护成本高。

对前端开发而言MVVM是非常好的一种设计模式。在浏览器中，路由层可以将控制权交由适当的ViewModel，后者又可以更新并响应持续的View，并且通过一些小修改MVVM模式可以很好的运行在服务器端，其中的原因就在于Model与View已经完全没有了依赖关系（通过View与Model的去耦合，可以允许短暂View与持续View的并存），这允许View经由给定的ViewModel进行渲染。


MVVM模型
1、M：模型(Model) ：对应data中的数据
2、V：视图(view) ：模板
3、VM：视图模型(ViewModel) : Vue实例对象

对应关系如下：

               ViewModel
     ------  DOM Listeners ----->
 View                           Model
   | <------- Data Bindings ------
   |              |              |
  DOM            Vue        Plain JavaScript Objects

观察发现：
   1、data中所有的属性，最后都出现在了vm身上。（vm是Vue的实例）
   2、vm身上所有的属性，及Vue原型上所有属性，在Vue模板中都可以直接使用。
                               

```
## 4、工程化（重点）
## 5、性能优化（重点）
## 6、TypeScript（暂时先不看）
## 7、网络（深入了解）
## 8、设计模式（先做了解）
## 9、数据结构/算法（暂时先不看）
## 10、算法（暂时先不看）
## 11、安全（先做了解）
## 12、Node（暂时先不看）
## 13、项目/业务（重点）
## 14、其他（重点）
