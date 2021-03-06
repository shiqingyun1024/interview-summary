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

   九宫格
   .box {
  display: flex;
  flex-wrap: wrap;
}
```
### 重绘和回流
```
https://blog.csdn.net/qq_38128179/article/details/101023305
1、HTML被HTML解析器解析成DOM Tree, CSS则被CSS解析器解析成CSSOM Tree。
2、DOM Tree和CSSOM Tree解析完成后，被附加到一起，形成渲染树（Render Tree）。
3、节点信息计算(重排)，这个过程被叫做Layout(Webkit)或者Reflow(Mozilla)。即根据渲染树计算每个节点的几何信息生成布局。
4、渲染绘制(重绘)，这个过程被叫做(Painting 或者 Repaint)。即根据计算好的信息绘制整个页面。
5、Display显示到屏幕上。

以上5步简述浏览器的一次渲染过程，每一次的dom更改或者css几何属性更改，都会引起一次浏览器的重排/重绘过程，而如果是css的非几何属性更改，则只会引起重绘过程。所以说重排一定会引起重绘，而重绘不一定会引起重排，重绘的开销较小，回流的代价较高。

二、重排（Relayout/Reflow）
所谓重排，实际上是根据渲染树中每个渲染对象的信息，计算出各自渲染对象的几何信息（DOM对象的位置和尺寸大小），并将其安置在界面中的正确位置。
重排也叫回流，实际上，reflow的字面意思也是回流，之所以有的叫做重排，也许是因为重排更好理解，更符合中国人的思维。标准文档之所以叫做回流（Reflow）,是因为浏览器渲染是基于“流式布局”的模型，流实际就使我们常说的文档流，当dom或者css几何属性发生改变的时候，文档流会受到波动联动的去更改，流就好比一条河里的水，回流就好比向河里扔了一块石头，激起涟漪，然后引起周边水流受到波及，所以叫做回流，这样理解似乎更标准更规范，不过叫什么并不重要，重要的是我们真正理解了这个过程就行。
重排的影响范围：
由于浏览器渲染界面是基于流式布局模型（Flow Based Layout），也就是某一个DOM节点信息更改了，就会触发重排。只是这个DOM更改程度会决定周边DOM更改范围，即全局范围和局部范围

全局范围：就是从根节点html开始对整个渲染树进行重新布局，例如当我们改变了窗口尺寸或方向或者是修改了根元素的尺寸或者字体大小等；

局部范围：对渲染树的某部分或某一个渲染对象进行重新布局。

会引起重排的操作：

页面首次渲染。
浏览器窗口大小发生改变——resize事件发生时。
元素尺寸或位置发生改变——定位、边距、填充、边框、宽度和高度。
元素内容变化（文字数量或图片大小等等）。
元素字体大小变化。
添加或者删除可见的DOM元素。
激活CSS伪类（例如：:hover）。
设置style属性
查询某些属性或调用某些方法。

常见引起重排属性和方法：

width
display
clientWidth
offsetWidth
scrollWidth
scrollIntoView()
getBoundingClientRect()
height
border
clientHeight
offsetHeight
scrollHeight
scrollTo()
scrollIntoViewIfNeeded()
margin
position
clientTop
offsetTop
scrollTop
getComputedStyle()
padding
overflow
clientLeft
offsetLeft
scrollLeft

三、重绘（Repainting）
相比重排，重绘就简单多了，所谓重绘，就是当页面中元素样式的改变并不影响它在文档流中的位置时，例如更改了字体颜色,浏览器会将新样式赋予给元素并重新绘制的过程称。
常见引起浏览器绘制过程的属性包含：
color
text-decoration
outline-color
outline-width
border-style
background-image
outline
box-shadow
visibility
background-position
outline-style
background-size
background
background-repeat
border-radius

四、优化策略
其实浏览器很聪明，不可能每次修改样式就 reflow 或者 repaint 一次，一般来说，浏览器会积累一批操作，然后做一次 reflow。但是我们也应该减少重绘重排的次数。

我们知道操作DOM是一个高成本的操作，不仅是因为本身js与DOM的链接访问，还包括操作DOM后引起一连串的重排，因此，从性能优化角度，我们可以从以下几个方面着手：

【1】减少DOM操作

最小化DOM访问次数，尽量缓存访问DOM的样式信息，避免过度触发回流。
如果在一个局部方法中需要多次访问同一个dom，可以在第一次获取元素时用变量保存下来，减少遍历时间。
用事件委托来减少事件处理器的数量。
用querySelectorAll()替代getElementByXX()。

2】减少重排

避免设置大量的style内联属性，因为通过设置style属性改变结点样式的话，每一次设置都会触发一次reflow，所以最好是使用class属性
不要使用table布局，因为table中某个元素一旦触发了reflow，那么整个table的元素都会触发reflow。那么在不得已使用table的场合，可以设置table-layout:auto;或者是table-layout:fixed这样可以让table一行一行的渲染，这种做法也是为了限制reflow的影响范围
尽量少使用display：none可以使用visibility：hidden代替，display：none会造成重排，visibility：hidden只会造成重绘。
使用resize事件时，做防抖和节流处理。

【3】css及优化动画

少用css表达式
减少通过JavaScript代码修改元素样式，尽量使用修改class名方式操作样式或动画；
可以把动画效果应用到position属性为absolute或fixed的元素上，这样对其他元素影响较小
动画实现的速度的选择。比如实现一个动画，以1个像素为单位移动这样最平滑，但是reflow就会过于频繁，大量消耗CPU资源，如果以3个像素为单位移动则会好很多。
开启动画的GPU加速，把渲染计算交给GPU。
```
### 垂直居中的方法
```
假设HTML代码如下，总共两个元素，父元素和子元素
<div class="wp">
    <div class="box size">123123</div>
</div>
/* 公共代码 */
.wp {
    border: 1px solid red;
    width: 300px;
    height: 300px;
}

.box {
    background: green;    
}

.box.size{
    width: 100px;
    height: 100px;
}
/* 公共代码 */

1、absolute + 负margin
/* 定位代码 */
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: 50%;
    left: 50%;
    margin-left: -50px;
    margin-top: -50px;
}

2、absolute + margin auto
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
3、absolute + calc
/* 此处引用上面的公共代码 */
/* 此处引用上面的公共代码 */

/* 定位代码 */
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: calc(50% - 50px);
    left: calc(50% - 50px);
}
4、absolute + transform
修复绝对定位的问题，还可以使用css3新增的transform，transform的translate属性也可以设置百分比，其是相对于自身的宽和高，所以可以讲translate设置为-50%，就可以做到居中了，代码如下：
.wp {
    position: relative;
}
.box {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
5、lineheight
/* 此处引用上面的公共代码 */
/* 此处引用上面的公共代码 */

/* 定位代码 */
.wp {
    line-height: 300px;
    text-align: center;
    font-size: 0px;
}
.box {
    font-size: 16px;
    display: inline-block;
    vertical-align: middle;
    line-height: initial;
    text-align: left; /* 修正文字 */
}

6、writing-mode
很多同学一定和我一样不知道writing-mode属性，感谢@张鑫旭老师的反馈，简单来说writing-mode可以改变文字的显示方向，比如可以通过writing-mode让文字的显示变为垂直方向

7、flex
.wp {
    display: flex;
    justify-content: center;
    align-items: center;
}
```
### BFC
```
BFC 即 Block Formatting Contexts (块级格式化上下文)
具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。

只要元素满足下面任一条件即可触发 BFC 特性：

body 根元素
浮动元素：float 除 none 以外的值
绝对定位元素：position (absolute、fixed)
display 为 inline-block、table-cells、flex
overflow 除了 visible 以外的值 (hidden、auto、scroll)
```
### CSS选择器
```
CSS的选择器其实大类的话可以分为三类，即id选择器、class选择器、标签选择器。它们之间可以有多种组合。

css选择器介绍：

1、类别选择器

类选择器根据类名来选择，前面以“.”来标志。

示例：

.demoDiv{
color:#FF0000;
}
2、标签选择器

一个完整的HTML页面是有很多不同的标签组成，而标签选择器，则是决定哪些标签采用相应的CSS样式。

在style.css文件中对p标签样式的声明如下：

p{
font-size:12px;
background:#900;
color:090;
}
3、ID选择器

ID 选择器可以为标有特定 ID 的 HTML 元素指定特定的样式。 根据元素ID来选择元素，具有唯一性，这意味着同一id在同一文档页面中只能出现一次。

前面以”#”号来标志，在样式里面可以这样定义：

#demoDiv{
color:#FF0000;
}
4、后代选择器

后代选择器也称为包含选择器，用来选择特定元素或元素组的后代，将对父元素的选择放在前面，对子元素的选择放在后面，中间加一个空格分开。

<style>
.father.child{
color:#0000CC;
}
</style>
<p class="father">
黑色
<label class="child">蓝色
<b>也是蓝色</b>
</label>
</p>
5、子选择器

请注意这个选择器与后代选择器的区别，子选择器（child selector）仅是指它的直接后代，或者你可以理解为作用于子元素的第一个后代。而后代选择器是作用于所有子后代元素。后代选择器通过空格来进行选择，而子选择器是通过“>”进行选择。

我们看下面的代码：

Example Source Code

CSS：

#links a {color:red;}
#links > a {color:blue;}
HTML：

<p id="links">
<a href="#">HTML中文网</a>>
<span><a href="#">CSS布局实例</a></span>
<span><a href="#">CSS教程</a></span>
</p>
6、伪类选择器

有时候还会需要用文档以外的其他条件来应用元素的样式，比如鼠标悬停等。这时候我们就需要用到伪类了。以下是链接应用的伪类定义。
爱恨原则”(LoVe/HAte),即四种伪类的首字母:LVHA
a:link，定义正常链接的样式；
a:visited，定义已访问过链接的样式；
a:hover，定义鼠标悬浮在链接上时的样式；
a:active，定义鼠标点击链接时的样式。


a:link{
color:#999999;
}
a:visited{
color:#FFFF00;
}
a:hover{
color:#006600;
}
/* IE不支持，用Firefox浏览可以看到效果 */
input:focus{
background:# E0F1F5;
}
7、通用选择器

通用选择器用*来表示。例如：

*{
font-size: 12px;
}
表示所有的元素的字体大小都是12px；同时通用选择器还可以和后代选择器组合。

8、群组选择器

当几个元素样式属性一样时，可以共同调用一个声明，元素之间用逗号分隔。如：

p, td, li {
line-height:20px;
color:#c00;
}
#main p, #sider span {
color:#000;
line-height:26px;
}
.#main p span {
color:#f60;
}
.text1 h1,#sider h3,.art_title h2 {
font-weight:100;
}
使用群组选择器，将会大大的简化CSS代码，将具有多个相同属性的元素，合并群组进行选择，定义同样的CSS属性，这大大的提高了编码效率，同时也减少了CSS文件的体积。

9、相邻同胞选择器

我们除了上面的子选择器与后代选择器，我们可能还希望找到兄弟两个当中的一个，如一个标题h1元素后面紧跟了两个段落p元素，我们想定位第一个段落p元素，对它应用样式。我们就可以使用相邻同胞选择器。
相邻同胞选择器使用了加号（+）

10、属性选择器

您可以用判断html标签的某个属性是否存在的方法来定义css。

属性选择器，是根据元素的属性来匹配的，其属性可以是标准属性也可以是自定义属性

11、伪元素选择器

所有伪元素选择器都必须放在出现该伪元素的选择器的最后面，也就是说伪元素选择器不能跟任何派生选择器。
::before 伪元素
::after 伪元素
::selection 伪元素

::after	p::after	在每个 <p> 元素之后插入内容。
::before	p::before	在每个 <p> 元素之前插入内容。
::first-letter	p::first-letter	选择每个 <p> 元素的首字母。
::first-line	p::first-line	选择每个 <p> 元素的首行。
::selection	p::selection	选择用户选择的元素部分。

伪类和伪元素的区别
css3规范中要求使用双冒号（::）表示伪元素，以此来区分伪类和伪元素，比如::before和::after等伪元素使用双冒号（::），:hover和:active伪类使用单冒号（:）

伪元素/伪对象：不存在在DOM文档中，是虚拟的元素，是创建新元素。代表某个元素的子元素，这个子元素虽然在逻辑上存在，但却并不实际存在于文档树中。

伪类：存在DOM文档中，逻辑上存在但在文档树中却无须标识的“幽灵”分类。

其中伪类和伪元素的根本区别在于：它们是否创造了新的元素。

伪类：用于向某些选择器添加特殊的效果
伪元素：用于将特殊的效果添加到某些选择器
其实根本意思就是就是对那些不能通过class、id等选择元素的补充
```
### rem
```
1、媒体查询
html {
    font-size : 20px;
}
@media only screen and (min-width: 401px){
    html {
        font-size: 25px !important;
    }
}
@media only screen and (min-width: 428px){
    html {
        font-size: 26.75px !important;
    }
}
@media only screen and (min-width: 481px){
    html {
        font-size: 30px !important; 
    }
}
@media only screen and (min-width: 569px){
    html {
        font-size: 35px !important; 
    }
}
@media only screen and (min-width: 641px){
    html {
        font-size: 40px !important; 
    }
}

2、或者使用js动态的计算屏幕的宽度，然后动态地给html的根元素赋值px的大小
https://blog.csdn.net/xiaolinlife/article/details/109288470
```
## 3、框架(Vue为主 重点)
### 生命周期各个阶段都做了什么？
```
https://blog.csdn.net/qq_47443027/article/details/114641639
vue 的生命周期是： vue 实例从创建到销毁，也就是从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、卸载等一系列过程。

每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做生命周期钩子的函数

beforeCreate	Function	在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。
created	Function	在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)， 属性和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，$el 属性目前不可见。
beforeMount	Function	在挂载开始之前被调用：相关的 render 函数首次被调用。
mounted	Function	el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 mounted 被调用时 vm.$el 也在文档内。
beforeUpdate	Function	数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器。该钩子在服务器端渲染期间不被调用，因为只有初次渲染会在服务端进行。
updated	Function	由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。
activated	Function	keep-alive 组件激活时调用。该钩子在服务器端渲染期间不被调用。
deactivated	Function	keep-alive 组件停用时调用。该钩子在服务器端渲染期间不被调用。
beforeDestroy	Function	实例销毁之前调用。在这一步，实例仍然完全可用。该钩子在服务器端渲染期间不被调用。
destroyed	Function	Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务器端渲染期间不被调用。
errorCaptured（2.5.0+ 新增）	(err: Error, vm: Component, info: string) => ?boolean	当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 false 以阻止该错误继续向上传播。


要掌握每个生命周期什么时候被调用

1.beforeCreate 在实例初始化之后，数据观测(data observer) 之前被调用。
2.created 实例已经创建完成之后被调用。在这一步，实例已完成以下的配置：数据观测(data observer)，属性和方法的运算，watch/event 事件回调。这里没有$el
3.beforeMount 在挂载开始之前被调用：相关的 render 函数首次被调用。
4.mounted el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子。
5.beforeUpdate 数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前。
6.updated 由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。
7.beforeDestroy 实例销毁之前调用。在这一步，实例仍然完全可用。
8.destroyed Vue 实例销毁后调用。调用后， Vue实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。 该钩子在服务器端渲染期间不被调用

要掌握每个生命周期内部可以做什么事

1.created 实例已经创建完成，因为它是最早触发的原因可以进行一些数据，资源的请求。
2.mounted 实例已经挂载完成，可以进行一些DOM操作
3.beforeUpdate 可以在这个钩子中进一步地更改状态，这不会触发附加的重渲染过程。
4.updated 可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态，因为这可能会导致更新无限循环。该钩子在服务器端渲染期间不被调用。
5.destroyed 可以执行一些优化操作,清空定时器，解除绑定事件

```
### $nextTick原理
```
https://www.cnblogs.com/liuhao-web/p/8919623.html  这一篇很全面。
https://blog.csdn.net/frontend_frank/article/details/105942284
Vue 在更新 DOM 时是异步执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部对异步队列尝试使用原生的 Promise.then、MutationObserver 和 setImmediate，如果执行环境不支持，则会采用 setTimeout(fn, 0) 代替。
也就是说我们在设置this.msg = 'some thing'的时候，Vue并没有马上去更新DOM数据，而是将这个操作放进一个队列中；如果我们重复执行的话，队列还会进行去重操作；等待同一事件循环中的所有数据变化完成之后，会将队列中的事件拿出来处理
这样做主要是为了提升性能，因为如果在主线程中更新DOM，循环100次就要更新100次DOM；但是如果等事件循环完成之后更新DOM，只需要更新1次。
为了在数据更新操作之后操作DOM，我们可以在数据变化之后立即使用Vue.nextTick(callback)；这样回调函数会在DOM更新完成后被调用，就可以拿到最新的DOM元素了。

nextTick源码分析

  了解了nextTick的用法和原理之后，我们就来看一下Vue是怎么来实现这波“操作”的。
Vue把nextTick的源码单独抽到一个文件中，/src/core/util/next-tick.js，删掉注释也就大概六七十行的样子，让我们逐段来分析。

我们首先找到nextTick这个函数定义的地方，看看它具体做了什么操作；看到它在外层定义了三个变量，有一个变量看名字就很熟悉：callbacks，就是我们上面说的队列；在nextTick的外层定义变量就形成了一个闭包，所以我们每次调用$nextTick的过程其实就是在向callbacks新增回调函数的过程。

callbacks新增回调函数后又执行了timerFunc函数，pending用来标识同一个时间只能执行一次。那么这个timerFunc函数是做什么用的呢，我们继续来看代码：

这里出现了好几个isNative函数，这是用来判断所传参数是否在当前环境原生就支持；例如某些浏览器不支持Promise，虽然我们使用了垫片(polify)，但是isNative(Promise)还是会返回false。

  可以看出这边代码其实是做了四个判断，对当前环境进行不断的降级处理，尝试使用原生的Promise.then、MutationObserver和setImmediate，上述三个都不支持最后使用setTimeout；降级处理的目的都是将flushCallbacks函数放入微任务(判断1和判断2)或者宏任务(判断3和判断4)，等待下一次事件循环时来执行。MutationObserver是Html5的一个新特性，用来监听目标DOM结构是否改变，也就是代码中新建的textNode；如果改变了就执行MutationObserver构造函数中的回调函数，不过是它是在微任务中执行的。

  那么最终我们顺藤摸瓜找到了最终的大boss：flushCallbacks；nextTick不顾一切的要把它放入微任务或者宏任务中去执行，它究竟是何方神圣呢？让我们来一睹它的真容：
本来以为有多复杂的flushCallbacks，居然不过短短的8行。它所做的事情也非常的简单，把callbacks数组复制一份，然后把callbacks置为空，最后把复制出来的数组中的每个函数依次执行一遍；所以它的作用仅仅是用来执行callbacks中的回调函数。

总结

到这里，整体nextTick的代码都分析完毕了，总结一下它的流程就是：
1、把回调函数放入callbacks等待执行
2、将执行函数放到微任务或者宏任务中
3、事件循环到了微任务或者宏任务，执行函数依次执行callbacks中的回调
再回到我们开头说的setTimeout，可以看出来nextTick是对setTimeout进行了多种兼容性的处理，宽泛的也可以理解为将回调函数放入setTimeout中执行；不过nextTick优先放入微任务执行，而setTimeout是宏任务，因此nextTick一般情况下总是先于setTimeout执行，我们可以在浏览器中尝试一下：
最后验证猜想，当前宏任务执行完成后，优先执行两个微任务，最后再执行宏任务。
```
### keep-alive
```

```
### 父子组件的执行顺序
```
加载渲染过程
->父beforeCreate -> 父created -> 父beforeMount 
->子beforeCreate -> 子created -> 子beforeMount -> 子mounted
-> 父mounted

子组件更新过程
->父beforeUpdate
-> 子beforeUpdate -> 子updated
-> 父updated

父组件更新过程
父beforeUpdate -> 父updated

销毁过程
-> 父beforeDestroy
-> 子beforeDestroy -> 子destroyed
-> 父destroyed

vue的生命周期

```
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
### 4.1 介绍一下Loader
```
1、常用的 loader 和它们的作用  use数组中loader执行顺序：从右到左，从下到上，依次执行。
style-loader 创建style标签，将js中的样式资源插入进去，添加到header中生成
css-loader 将css文件变成commonjs模块加载到js中，里面内容是样式字符串
less-loader 将less文件转化为css文件
sass-loader 将sass文件转化为css文件
vue-loader 解析和转换 .vue 文件，提取出其中的逻辑代码 script、样式代码 style、以及 HTML 模版 template，再分别把它们交给对应的 Loader 去处理。
html-loader 处理html文件中的img图片，负责引入img，从而能被url-loader进行处理。
url-loader 下载url-loader file-loader   url-loader要依赖于file-loader，所以也要下载file-loader
file-loader 分发文件到 output 目录并返回相对路径,打包其他资源主要是包括字体图标等，主要用file-loader来处理这些资源，原理一般都是直接拷贝到打包库里面，不进行任何转化处理
url-loader 和 file-loader 类似，但是当文件小于设定的 limit 时可以返回一个 Data Url
html-minify-loader 压缩 HTML
babel-loader 用 babel 来转换 ES6 文件到 ES5

主要用到了style-loader，css-loader，less-loader，从右往左，或者从下往上执行，
less-loader是把less文件转化为css文件，
css-loader是把css文件转化为js文件，
style-loader是把css样式通过添加style标签的形式引入到html中。

{
              // 匹配哪些文件
              test:/\.less$/,
              // 使用哪些loader进行处理
              use:[
                // use数组中loader执行顺序：从右到左，从下到上，依次执行。
                //   创建style标签，将js中的样式资源插入进去，添加到header中生成
                  'style-loader',
                //   将css文件变成commonjs模块加载到js中，里面内容是样式字符串
                'css-loader',
                // 将less文件转化为css文件
                // 需要下载less-loader和less
                'less-loader'
              ]  
}

// 处理图片资源处理css、sass、less等文件中的img图片 不过这个loader处理不了在html中img图片，例如：<img src="./images/vue1.jpeg" />，先要用html-loader负责先引入图片，然后由url-loader处理。
            //  url-loader是处理文件中引入的图片路径的，如：background-image: url('./images/vue1.jpeg');  
            {
                test: /\.(jpg|png|jpeg|gif)$/,
                // 使用单个loader
                // 下载url-loader file-loader   url-loader要依赖于file-loader，所以也要下载file-loader
                loader: 'url-loader',
                // 进行配置
                options: {
                    // 图片大小小于8kb，就会被base64处理（一般都处理8~12kb及以下的图片）
                    // 优点：减少请求数量（减轻服务器压力）
                    // 缺点：图片体积会更大（文件请求速度更慢）
                    limit: 10 * 1024,
                    // 问题：因为url-loader默认使用es6模块化解析，而html-loader引入图片是commonjs
                    // 解析时会出现问题，[object Module]
                    // 解决：关闭url-loader的es6模块化，使用commonjs解析
                    esModule:false,
                    // 给图片进行重命名
                    // [hash:8]取图片的hash的前8位
                    // [ext]取文件原来的扩展名
                    name:'[hash:8].[ext]',
                    // 输出到build下面的imgs文件夹中
                    outputPath:'imgs'
                },
            },

            {
                test: /\.html$/,
                // 处理html文件中的img图片，负责引入img，从而能被url-loader进行处理。
                loader: 'html-loader'
            }
打包其他资源主要是包括字体图标等，主要用file-loader来处理这些资源，原理一般都是直接拷贝到打包库里面，不进行任何转化处理。
同时使用clean-webpack-plugin来清除build文件夹，这样每次打包，里面都是最新的打包文件，把之前的删除掉了。
通过cleanAfterEveryBuildPatterns来设置打包时清空的文件夹。
cleanAfterEveryBuildPatterns:['build']

```
### 4.2 webpack相关的优化
```
1、通过exclude、include 缩小搜索范围
module.exports = {
    module:{
        rules:[
            {
                test:/\.js$/,
                loader:'babel-loader',
                // 只在src文件夹中查找
                include:[resolve('src')],
                // 排除的路径
                exclude:/node_modules/
            }
        ]
    }
}

2、减少不必要的模块依赖
module.noParse字段可用于配置不需要解析的模块。对于类似jquery和lodash这些大型的第三方库，本身就是兼容性非常好的，不需要在进行解析。可以忽略以提升构建速度。
module.exports = {
  // ...
  module: {
    noParse: /jquery|lodash/, // 正则表达式

    // 或者使用 function
    noParse(content) {
      return /jquery|lodash/.test(content)
    },
  }
}
3、使用cache-loader进行缓存文件。
对于一些比较消耗性能的模块，并且打包后文件内容没有改动可以使用cache-loader进行缓存。因为缓存是直接保存在硬盘,所以存取缓存都是要消耗一定的性能。小文件没必要缓存。下次就可以不使用loader而使用缓存。
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          {
            loader: 'cache-loader',
            options: {
              cacheDirectory: path.resolve('.cache') //还能进行配置
            }
          },
          'babel-loader'
        ],
        include: path.resolve('src')
      }
    ]
  }
}
4、开启多线程构建
webpack是基于node.js运行的，而node.js是单线程的，所以webpack处理文件是一个个处理的，如果需要处理的文件一多的话，处理速度会很慢，因此在需要解析大量文件的项目中，可以通过开启多线程来处理文件，以加快构建速度。
loader
如thread-loader会开启一个线程池，线程池中包含适量的线程，利用多线程同时开启多个loader的处理
// webpack.config.js 文件配置
module.exports = {
  mode: "development",
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          "thread-loader",
          "babel-loader"
        ]
      }
    ]
  }
};

plugin
可以使用happypack是一个开启多线程解析和构建文件的plugin。
var HappyPack = require('happypack')
var happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length })
module.exports = {
  mode: "development",
  plugins: [
  new HappyPack({
    id: 'js',
    loaders: ['babel-loader'],
    // 用于检索工作线程的预定义线程池
    threadPool: happyThreadPool,
    // 启用此选项可将状态消息从HappyPack记录到STDOUT
    verbose: true
  })
]

开发阶段的页面响应速度
当webpack开启watch，当文件被修改后,webpack会自动构建输出新的打包文件，但是还需要刷新浏览器重载页面才能看到最新的内容，这样的会降低开发效率。webpack-dev-server正好适用于处理类似的问题。webpack-dev-server 的热更新和热替换功能能提高我们开发阶段的页面响应速度。
1.热更新
热更新可以理解为当文件有修改，页面会实时刷新。原理是,webpack-dev-server使用的是express框架作为文件资源的http服务器，通过websoket协议和浏览器端进行通讯。当文件修改的时候，http服务器会监听到文件变化，而触发webpakc编译文件，但是文件内容不会输出到output的目录，而是把编译的内容存放到内存中，然后浏览器就会实时更新内存中的内容，实现页面实时更新。
2.热替换HMR（Hot Module Replacement）
热更新是把整个页面刷新，而热替换是只刷新修改的那部分。css样式修改可以立马看到热替换效果，因为css只是样式。而如js这些逻辑性的代码，无法页面热替换，除了css文件都需要额外的手动处理才能热替换。

// webpack.config.js
const webpack = require('webpack')
 
module.exports = {
  // ...
  devServer: {
    // 开启 HMR 特性，如果资源不支持 HMR 会 fallback 到 live reloading
    hot: true
    // 只使用 HMR，不会 fallback 到 live reloading
    // hotOnly: true
  },
  plugins: [
    // ...
    // HMR 特性所需要的插件
    new webpack.HotModuleReplacementPlugin()
  ]
}

如何优化打包后的文件
打包后的文件最优的结果就是文件尽可能少、文件尽可能小，所以只需要根据这两个方向去优化。

1.模块分离
模块分离就是将一个整体的代码，分布到不同的打包文件中去，目的就是为了减少公共代码，降低打包文件总体积，特别是对于一些大型的第三方库，使用.模块分离还能充分利用浏览器缓存去加载这些公共模块，不用每次都去请求，利于传输速度。当多个chunk引入了公共模块的时候，webpack默认是不分离模块的，这样就会造成代码重复，所以当多个chunk引入了公共模块或者公共模块体积比较大，或者公共模块较少变动的时候，可以使用分包。
分包有两种方式：手动分离和自动分离

手动分离
手动分包就是开发人员通过根据分析模块的依赖关系，把公共模块通过打包或手动放到一个公共文件夹里，被打包后的index.html文件引入到页面。
大致步骤：
（1）提取公共模块到公共文件夹，并生成资源清单列表（可以手动创建，也可以使用webpack内置插件DllPlugin生成）
（2）在html页面引入公共模块
（3）使用DllReferencePlugin控制chunk打包结果,资源清单里面有的模块，不在打包进入bundle里面。

自动分离
自动分离相对于手动分离会方便一点，只需要配置要分离策略。
webpack提供了optimization配置项，用于配置一些优化信息
其中splitChunks是分离模块策略的配置
module.exports = {
  optimization: {
    splitChunks: {
      // 分包策略
      chunks: 'all',
    }
  }
}

2.代码压缩
webpack自动集成了Terser插件进行打包时代码压缩,只需要配置optimization选项即可
/压缩js
const TerserPlugin = require('terser-webpack-plugin');
//压缩css
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin'); 
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: ExtractTextPlugin.extract('style-loader', 'css-loader')
      }
    ]
  },
  optimization: {
    // 是否要启用压缩，默认情况下，生产环境会自动开启
    minimize: true, 
    minimizer: [ // 压缩时使用的插件，可以有多个
      new TerserPlugin(), 
      new OptimizeCSSAssetsPlugin()
    ],
  },
};

3.删除无用的代码和模块
webpack2开始内置Tree-Shaking功能来删除用不到或者对程序没有影响的代码，极大的减少了打包后的体积。tree-shaking是基于es6的，因此对于一些使用commonJs导出的第三方库如lodash，应该换成es6方式导出的版本，这样才能使用tree-shaking优化。

4.模块懒加载
对于一些不会立即使用的模块，可以通过懒加载的形式导入import('xxx'),webpack打包的时候会把这些需要懒加载的模块打包成独立的文件，只有当使用到的时候才会去加载这个文件，并把模块添加到webpackJsonp对象中

采用Oneof

Scope Hoisting
原理
作用域提升，用来分析模块间的依赖关系，尽可能将打散的模块合并到一个函数中，又不能造成代码冗余，所以只有被引用一次的模块才能被合并。 由于需要分析模块间的依赖关系，所以源码IXUS采用了ES6模块化的，否则Webpack会降级不采用 Scope Hoisting。

```
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
