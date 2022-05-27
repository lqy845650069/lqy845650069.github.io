# CPP Everyday 

本[Page](https://lqy845650069.github.io)建立于2022.5.24 23:00，主要用于帮助自己记忆一些CPP知识点

知识点并没有按照特定顺序，可通过目录查询

## Day 0 static关键字
static关键字可分为类内static，类外static

### 类内static
类内static主要表示在类的实例间共享的变量/函数，类内static变量需要在**类外**初始化，类内static函数**没有this指针**。那么显然，类的静态函数不能访问非静态的变量/函数。

### 类外static
类外static可以分为局部static和全局static
####  局部static
局部static变量的生存期与整个程序相同，但只对于当前作用域可见，该变量在第一次执行到的时候进行初始化，之后将跳过此语句。C++11后C++保证了static的线程安全，因此可以通过static来实现单例模式
```C++
class Singleton
{
public:
	static Singleton& Get()
	{
		static Singleton instance;
		return instance;
	}
};
```

####  全局static
全局static主要用于表示只对当前文件可见，即此变量\函数只会在当前的翻译单元（cpp文件）内部链接，防止链接时的**重定义**（变量或者函数定义，单纯的函数声明不会引起重定义）。所以编码时尽量少用全局变量，对于全局变量可以用 `const` 或者 `static` 进行修饰防止重定义

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

## Day 2 mutable关键字
- 当方法是 `const` 时，还是可以修改被 `mutable` 关键字修饰的变量
- 当使用lambda表达式时，当没有 `mutable` 关键字时，lambda表达式只能使用 `=` 值传递的变量（引用传递无需 `mutable` ），加上 `mutable` 后可以对变量进行修改，但不会对外部的实参产生影响（和函数传参一样）

## Day 3 C++的四个显示转换
- `static_cast` 当方法是 `const` 时，还是可以修改被 `mutable` 关键字修饰的变量
- `dynamic_cast` 是当我们想要做一些（沿继承层次结构的）强制类型转换时，C++提供的一种安全的转换， `dynamic_cast` 通过运行时的类型检查，确保我们实际的转换是有效的，安全的
```C++
class Entity{
};
class Player: public Entity{
	int hp;
};
Entity *e = new Entity;
Player *p = new Player;
e = dynamic_cast<Entity*>(p);
p = dynamic_cast<Player*>(e); // return nullptr;
```
