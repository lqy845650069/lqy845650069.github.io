# CPP Everyday 

本Page建立于2022.5.24 23:00，主要用于帮助自己记忆一些CPP知识点
知识点并没有按照特定顺序，可通过目录查询

## Day 0 static关键字
static关键字可分为类内static，类外static
### 类内static
类内static主要表示在类的实例间共享的变量/函数，类内static变量需要在**类外**初始化，类内static函数**没有this指针**
### 类外static
类外static可以分为局部static和全局static
#### 局部static
局部static变量的生存期与整个程序相同，但只对于当前作用域可见，该变量在第一次执行到的时候进行初始化，之后将跳过此语句
#### 全局static
全局static主要用于表示只对当前文件可见，防止链接时的**重定义**（变量或者函数定义，单纯的函数声明不会引起重定义）
### static和const能否同时使用
不能，函数的角度来看，const实际上是隐含着将this指针变为const指针（指向的对象不变），而static修饰的函数没有this指针
