# CPP Everyday

- [CPP Everyday](#cpp-everyday)
	- [Day 0 static关键字](#day-0-static关键字)
		- [类内static](#类内static)
		- [类外static](#类外static)
			- [局部static](#局部static)
			- [全局static](#全局static)
		- [static和const能否同时使用](#static和const能否同时使用)
	- [Day 1 const关键字](#day-1-const关键字)
		- [类外的const](#类外的const)
		- [类中的const](#类中的const)
	- [Day 2 mutable关键字](#day-2-mutable关键字)
	- [Day 3 C++的四个显示转换](#day-3-c的四个显示转换)
	- [Day 4 this指针](#day-4-this指针)
	- [Day 5 C++线程1](#day-5-c线程1)

本[Page](https://lqy845650069.github.io)建立于2022.5.24 23:00，主要用于帮助自己记忆一些CPP知识点

知识点并没有按照特定顺序，可通过目录查询

## Day 0 static关键字

`static`关键字可分为类内`static`，类外`static`

### 类内static

类内`static`主要表示在类的实例间共享的变量/函数，类内`static`变量需要在**类外**初始化，类内`static`函数**没有`this`指针**。那么显然，类的静态函数不能访问非静态的变量/函数。

### 类外static

类外`static`可以分为局部 `static` 和全局`static`

#### 局部static

局部`static`变量的生存期与整个程序相同，但只对于当前作用域可见，该变量在第一次执行到的时候进行初始化，之后将跳过此语句。C++11后C++保证了`static`的线程安全，因此可以通过`static`来实现单例模式

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

#### 全局static

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

- `static_cast` 静态类型转换，做编译时检查，可以实现C++中内置基本数据类型之间的相互转换，enum、struct、 int、char、float等，能进行类层次间的向上类型转换和向下类型转换（向下不安全，因为没有进行动态类型检查）。它不能进行无关类型(如非基类和子类)指针之间的转换，也不能作用包含底层const的对象
- `dynamic_cast` 是当我们想要做一些（沿继承层次结构的指针或引用）强制类型转换时，C++提供的一种安全的转换， `dynamic_cast` 通过运行时的类型检查，确保我们实际的转换是有效的，安全的，若指针转换失败返回NULL，若引用返回失败抛出bad_cast异常。使用dynamic_cast父类一定要有虚函数，否则编译不通过

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

- `reinterpret_cast` 此标识符的意思即为将数据的二进制形式重新解释，但是不改变其值，有着和C风格的强制转换同样的能力。它可以转化任何内置的数据类型为其他任何的数据类型，也可以转化任何指针类型为其他的类型。它甚至可以转化内置的数据类型为指针，无须考虑类型安全或者常量的情形，不安全
- `const_cast` 移除或者添加 `const` 属性，`const_cast` 只能用于指针或引用，并且只能改变对象的底层 `const` （顶层 `const` ，本身是 `const` ，底层 `const` ，指向对象const）

## Day 4 this指针

`this`是一个指向当前对象实例的指针，并且属于当前对象。`this`指针是一个顶层`const`，形如`Entity* const this`，当当前函数为`const`是，`this`指针同时也是一个底层`const`，形如`const Entity* const this`

## Day 5 C++线程1

```C++
#include<iostream>
#include<thread>
static bool s_Finished = false;
void DoWork(){
	while(!s_Finished){
	std::cout<<"Working\n";
	}
}

int main(){
	std::thread worker(DoWork);
	std::cin.get(); // Keep worker working
	s_Finished = true; // Stop worker working
	worker.join();
	std::cout<<"Finished !\n";
	std::cin.get();
	return 0;
}
```

`thread`来新建一个线程并且开始执行传入的函数指针`DoWork`, `join()`阻塞了主线程，等待分支线程执行结束并且回收资源，也可以通过`detach()`让两个线程互相独立，分支线程的资源将在分支线程结束后由守护线程回收资源


## Day 6 拷贝函数的调用时机
拷贝构造函数被调用的几种情况：
- 当用类的一个对象去初始化该类的另一个对象时，系统会自动调用拷贝构造函数；
- 将一个对象作为实参传递给一个非引用类型的形参，系统会自动调用拷贝构造函数；
- 从一个返回类为非引用的函数返回一个对象时，系统会自动调用拷贝构造函数；
- 用花括号列表初始化一个数组的元素时，系统会自动调用拷贝构造函数。
