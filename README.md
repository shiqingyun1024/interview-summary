# interview-summary
关于面试题的一些总结
## 1、JavaScript基础（重点）
#### 1.1 执行上下文/作用域链/闭包
```
```

## 2、CSS基础（重点）

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
