# interview-summary
关于面试题的一些总结

## 3、框架(Vue为主)
### MVVM 
#### 介绍一下MVVM，和MVC有什么区别
```
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
