# C++对C的扩展



[TOC]



## 一、::作用运算符

通常情况下，如果有两个同名变量，一个是全局变量，另一个是局部变量，那么局部变量在其作用域内具有较高的优先权，它将屏蔽全局变量。

```c++
//全局变量
int a = 10;
void test(){
	//局部变量
	int a = 20;
	//全局a被隐藏
	cout << "a:" << a << endl;
}
```

程序的输出结果是a:20。在test函数的输出语句中，使用的变量a是test函数内定义的局部变量，因此输出的结果为局部变量a的值。

作用域运算符可以用来解决局部变量与全局变量的重名问题 

```c++
//全局变量
int a = 10;
//1. 局部变量和全局变量同名
void test(){
	int a = 20;
	//打印局部变量a
	cout << "局部变量a:" << a << endl;
	//打印全局变量a
	cout << "全局变量a:" << ::a << endl;
}

```

这个例子可以看出，作用域运算符可以用来解决局部变量与全局变量的重名问题，即在局部变量的作用域内，可用::对被屏蔽的同名的全局变量进行访问。

## 二、名字控制

创建名字是程序设计过程中一项最基本的活动，当一个项目很大时，它会不可避免地包含大量名字。c++允许我们对名字的产生和名字的可见性进行控制。

我们之前在学习c语言可以通过static关键字来使得名字只得在本编译单元内可见，在c++中我们将通过一种通过命名空间来控制对名字的访问。

### 1、C++命名空间(namespace)

在c++中，名称（name）可以是符号常量、变量、函数、结构、枚举、类和对象等等。工程越大，名称互相冲突性的可能性越大。另外使用多个厂商的类库时，也可能导致名称冲突。为了避免，在大规模程序的设计中，以及在程序员使用各种各样的C++库时，这些标识符的命名发生冲突，标准C++引入关键字namespace（命名空间/名字空间/名称空间），可以更好地控制标识符的作用域。

### 2、命名空间使用语法

#### a.创建一个命名空间

```c++
namespace A{
	int a = 10;
}
namespace B{
	int a = 20;
}
void test(){
	cout << "A::a : " << A::a << endl;
	cout << "B::a : " << B::a << endl;
}
```

#### b.命名空间只能全局范围内部定义

**以下写法错误**

```c++
void test(){
	namespace A{
		int a = 10;
	}
	namespace B{
		int a = 20;
	}
	cout << "A::a : " << A::a << endl;
	cout << "B::a : " << B::a << endl;
}
```

#### b.命名空间可以嵌套

```c++
namespace A{
	int a = 10;
	namespace B{
		int a = 20;
	}
}
void test(){
	cout << "A::a : " << A::a << endl;
	cout << "A::B::a : " << A::B::a << endl;
}
```

#### c.命名空间是开放的，即可以随时把新的成员加入已有的命名空间中

```c++
namespace A{
	int a = 10;
}

namespace A{
	void func(){
		cout << "hello namespace!" << endl;
	}
}

void test(){
	cout << "A::a : " << A::a << endl;
	A::func();
}
```

#### d.声明和实现可分离

```c++
namespace MySpace{
	void func1();
	void func2(int param);
}
void MySpace::func1(){
	cout << "MySpace::func1" << endl;
}
void MySpace::func2(int param){
	cout << "MySpace::func2 : " << param << endl;
}
```

#### e.无名命名空间，意味着命名空间中的标识符只能在本文件内访问，相当于给这个标识符加上了static，使得其可以作为内部连接

```c++
namespace{
	int a = 10;
	void func(){ cout << "hello namespace" << endl; }
}
void test(){
	cout << "a : " << a << endl;
	func();
}
```

#### f.命名空间别名

~~~c++
namespace veryLongName{
	int a = 10;
	void func(){ cout << "hello namespace" << endl; }
}

void test(){
	namespace shortName = veryLongName;
	cout << "veryLongName::a : " << shortName::a << endl;
	veryLongName::func();
	shortName::func();
}
~~~

### 3、using声明

using声明可使得指定的标识符可用。

```c++
namespace A{
	int paramA = 20;
	int paramB = 30;
	void funcA(){ cout << "hello funcA" << endl; }
	void funcB(){ cout << "hello funcA" << endl; }
}

void test(){
	//1. 通过命名空间域运算符
	cout << A::paramA << endl;
	A::funcA();
	//2. using声明
	using A::paramA;
	using A::funcA;
	cout << paramA << endl;
	//cout << paramB << endl; //不可直接访问
	funcA();
	//3. 同名冲突
	//int paramA = 20; //相同作用域注意同名冲突
}
```

using声明碰到函数重载

```c++
namespace A{
	void func(){}
	void func(int x){}
	int  func(int x,int y){}
}
void test(){
	using A::func;
	func();
	func(10);
	func(10, 20);
}
```

如果命名空间包含一组用相同名字重载的函数，using声明就声明了这个重载函数的所有集合。

### 4、using编译指令

using编译指令使整个命名空间标识符可用.

```c++
namespace A{
	int paramA = 20;
	int paramB = 30;
	void funcA(){ cout << "hello funcA" << endl; }
	void funcB(){ cout << "hello funcB" << endl; }
}

void test01(){
	using namespace A;
	cout << paramA << endl;
	cout << paramB << endl;
	funcA();
	funcB();

	//不会产生二义性
	int paramA = 30;
	cout << paramA << endl;
}

namespace B{
	int paramA = 20;
	int paramB = 30;
	void funcA(){ cout << "hello funcA" << endl; }
	void funcB(){ cout << "hello funcB" << endl; }
}

void test02(){
	using namespace A;
	using namespace B;
	//二义性产生，不知道调用A还是B的paramA
	//cout << paramA << endl;
}
```

 **注意：使用using声明或using编译指令会增加命名冲突的可能性。也就是说，如果有名称空间，并在代码中使用作用域解析运算符，则不会出现二义性。**

### 5、名称空间使用

需要记住的关键问题是**当引入一个全局的using编译指令时，就为该文件打开了该命名空间，它不会影响任何其他的文件**，所以可以在每一个实现文件中调整对命名空间的控制。比如，如果发现某一个实现文件中有太多的using指令而产生的命名冲突，就要对该文件做个简单的改变，通过明确的限定或者using声明来消除名字冲突，这样**不需要修改其他的实现文件**。

## 三、struct类型加强

- c中定义结构体变量需要加上struct关键字，c++不需要。
- c中的结构体只能定义成员变量，不能定义成员函数。c++即可以定义成员变量，也可以定义成员函数。

```c++
//1. 结构体中即可以定义成员变量，也可以定义成员函数
struct Student{
	string mName;
	int mAge;
	void setName(string name){ mName = name; }
	void setAge(int age){ mAge = age; }
	void showStudent(){
		cout << "Name:" << mName << " Age:" << mAge << endl;
	}
};

//2. c++中定义结构体变量不需要加struct关键字
void test01(){
	Student student;//前面不需要加struct
	student.setName("John");
	student.setAge(20);
	student.showStudent();
}
```

## 四、更严格的类型转换

在C++，不同类型的变量一般是不能直接赋值的，需要相应的强转。

c语言代码:

```c
typedef enum COLOR{ GREEN, RED, YELLOW } color;
int main(){

	color mycolor = GREEN;
	mycolor = 10;
	printf("mycolor:%d\n", mycolor);
	char* p = malloc(10);
	return EXIT_SUCCESS;
}
```

**以上c代码c编译器编译可通过，c++编译器无法编译通过。**

## 五、三目运算符功能增强

- c语言三目运算表达式返回值为数据值，为右值，不能赋值。

```c
	int a = 10;
	int b = 20;
	printf("ret:%d\n", a > b ? a : b);
	//思考一个问题，(a > b ? a : b) 三目运算表达式返回的是什么？
	
	//(a > b ? a : b) = 100;
	//返回的是右值

```

- c++语言三目运算表达式返回值为变量本身(引用)，为左值，可以赋值。

```c++
	int a = 10;
	int b = 20;
	printf("ret:%d\n", a > b ? a : b);
	//思考一个问题，(a > b ? a : b) 三目运算表达式返回的是什么？

	cout << "b:" << b << endl;
	//返回的是左值，变量的引用
	(a > b ? a : b) = 100;//返回的是左值，变量的引用
	cout << "b:" << b << endl;
```

[^左值和右值]: 在c++中可以放在赋值操作符左边的是左值，可以放到赋值操作符右面的是右值

## 六、C/C++中的const

### 1、const概述

const单词字面意思为常数，不变的。它是c/c++中的一个关键字，是一个限定符，它用来限定一个变量不允许改变，它将一个对象转换成一个常量。

```c++
const int a = 10;
A = 100; //编译错误,const是一个常量，不可修改
```

### 2、C/C++中const的区别

#### a.C中的const

常量的引进是在c++早期版本中，当时标准C规范正在制定。那时，尽管C委员会决定在C中引入const,但是，他们c中的const理解为”一个不能改变的普通变量”，也就是认为const应该是一个只读变量，既然是变量那么就会给const分配内存，并且在c中const是一个全局只读变量，c语言中const修饰的只读变量是外部连接的。

如果这么写:

```c
const int arrSize = 10;
int arr[arrSize];
```

看似是一件合理的编码，但是这将得出一个错误。 因为arrSize占用某块内存，所以C编译器不知道它在编译时的值是多少

#### b.C++中的const

- **在c++中，一个const不必创建内存空间，而在c中，一个const总是需要一块内存空间。**在c++中，是否为const常量分配内存空间依赖于如何使用。一般说来，如果一个const仅仅用来把一个名字用一个值代替(就像使用#define一样)，那么该存储局空间就不必创建。

- 如果存储空间没有分配内存的话，在进行完数据类型检查后，为了代码更加有效，值也许会折叠到代码中。

- 不过，取一个const地址, 或者把它定义为extern,则会为该const创建内存空间。

- 在c++中，出现在所有函数之外的const作用于整个文件(也就是说它在该文件外不可见)，默认为内部连接，c++中其他的标识符一般默认为外部连接。

#### c.C/C++中const异同总结

- 

