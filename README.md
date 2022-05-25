# CPP Everyday 

本[Page](lqy845650069.github.io)建立于2022.5.24 23:00，主要用于帮助自己记忆一些CPP知识点

知识点并没有按照特定顺序，可通过目录查询

## Day 0 static关键字
static关键字可分为类内static，类外static

### 类内static
类内static主要表示在类的实例间共享的变量/函数，类内static变量需要在**类外**初始化，类内static函数**没有this指针**

### 类外static
类外static可以分为局部static和全局static
####  局部static
局部static变量的生存期与整个程序相同，但只对于当前作用域可见，该变量在第一次执行到的时候进行初始化，之后将跳过此语句
####  全局static
全局static主要用于表示只对当前文件可见，防止链接时的**重定义**（变量或者函数定义，单纯的函数声明不会引起重定义）

### static和const能否同时使用
不能，函数的角度来看，const实际上是隐含着将this指针变为const指针（指向的对象不变），而static修饰的函数没有this指针

## Day 1 const关键字

### 类外的const
const更像是一个承诺，承诺了一些东西是不变的
```C++
const int MAX_AGE = 5;
```
表示我们承诺`MAX_AGE`是不可变的，但我们可以通过一些方式（比如指针）来违背这种承诺
```C++
int* a = (int*)(&MAX_AGE);
*a = 2;
```
但这样做显然是不对的\会引起程序崩溃的\引起各种bug的，那么我们可以通过给指针也添加const关键字来限制对其指向地址或者其指向的内容的修改
`const int*` 和 `int const *` 表示了同样的意思，即我们承诺不修改指针指向的对象，而 `int* const` 则表示我们承诺不修改这个指针变量所保存的地址，可以通过 `const` 在 `*` 的前后来区分这两种

### 类中的const
C++规定可以通过const重载函数，类的const对象只能调用const函数，不能调用非const函数，而非const对象在调用函数时，会优先调用表明const的函数（如果有）

### mutable关键字
TODO

