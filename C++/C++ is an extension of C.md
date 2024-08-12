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

- **c语言全局const会被存储到只读数据段**。c++中全局const当声明extern或者对变量取地址时，编译器会分配存储地址，变量存储在只读数据段。两个都受到了只读数据段的保护，不可修改。

```c++
const int constA = 10;
      int main(){
           int* p = (int*)&constA;
           *p = 200;
     }
```

以上代码在c/c++中编译通过，在运行期，修改constA的值时，发生写入错误。原因是修改只读数据段的数据。

- **c语言中局部const存储在堆栈区**，只是不能通过变量直接修改const只读变量的值，但是可以跳过编译器的检查，通过指针间接修

  改const值。

```c++
const int constA = 10;
	int* p = (int*)&constA;
	*p = 300;
	printf("constA:%d\n",constA);
	printf("*p:%d\n", *p);
```

运行得到修改后的结果

c语言中，通过指针间接赋值修改了constA的值。

c++中对于局部的const变量要区别对待：

1. 对于基础数据类型，也就是const int a = 10这种，编译器会进行优化，将值替换到访问的位置。

   ```c++
   const int constA = 10;
   	int* p = (int*)&constA;
   	*p = 300;
   	cout << "constA:" << constA << endl;
   	cout << "*p:" << *p << endl;
   //运行结果 constA:10 *p:300
   ```

2. 对于基础数据类型，如果用一个变量初始化const变量，如果const int a = b,那么也是会给a分配内存。

   ```c++
   	int b = 10;
   	const int constA = b;
   	int* p = (int*)&constA;
   	*p = 300;
   	cout << "constA:" << constA << endl;
   	cout << "*p:" << *p << endl;
   //运行结果 constA:300 *p:300
   //constA 分配了内存，所以我们可以修改constA内存中的值。
   ```

3. 对于自定数据类型，比如类对象，那么也会分配内存。

   ```c++
       const Person person; //未初始化age
   	//person.age = 50; //不可修改
   	Person* pPerson = (Person*)&person;
   	//指针间接修改
   	pPerson->age = 100;
   	cout << "pPerson->age:" << pPerson->age << endl;
   	pPerson->age = 200;
   	cout << "pPerson->age:" << pPerson->age << endl;
   //运行结果 pPerson->age:100 pPerson->age:200
   //为person分配了内存，我们可以通过指针间接赋值修改person对象
   ```

- **c中const默认为外部连接，c++中const默认为内部连接**.当c语言两个文件中都有const int a的时候，编译器会报重定义的错误。而在c++中，则不会，因为c++中的const默认是内部连接的。**如果想让c++中的const具有外部连接，必须显示声明为: extern const int a = 10**;

const由c++采用，并加进标准c中，尽管他们很不一样。在c中，编译器对待const如同对待变量一样，只不过带有一个特殊的标记，意思是”你不能改变我”。在c中定义const时，编译器为它创建空间，所以如果在两个不同文件定义多个同名的const，链接器将发生链接错误。简而言之,const在c++中用的更好。

- 能否用变量定义数组

在vs中不支持C99

但正在linux gcc支持C99编译器通过

```c
int a = 10;
int arr[a];
int i = 0;
for(;i<10;i++) 
	arr[i] = i;
i = 0;
for(;i<10;i++)
	printf("%d\n",arr[i]);
```

### 3、尽量以const替换#define

在旧版本C中，如果想建立一个常量，必须使用预处理器”

\#define MAX 1024;// const int max = 1024

我们定义的宏MAX从未被编译器看到过，因为在预处理阶段，所有的MAX已经被替换为了1024，于是MAX并没有将其加入到符号表中。但我们使用这个常量获得一个编译错误信息时，可能会带来一些困惑，因为这个信息可能会提到1024，但是并没有提到MAX.如果MAX被定义在一个不是你写的头文件中，你可能并不知道1024代表什么，也许解决这个问题要花费很长时间。

解决办法就是用一个常量替换上面的宏。

const int max= 1024;

- const和#define区别总结:

对const来说:

  1．const有类型，可进行编译器类型安全检查。#define无类型，不可进行类型检查.  2．const有作用域，而#define不重视作用域，默认定义处到文件结尾.如果定义在指定作用域下有效的常量，那么#define就不能用。  

对#difine来说:

1. 宏常量没有类型，所以调用了int类型重载的函数。const有类型，所以调用希望的short类型函数

   ```c++
   #define PARAM 128
   const short param = 128;
   
   void func(short a){
   	cout << "short!" << endl;
   }
   void func(int a){
   	cout << "int" << endl;
   }
   ```

2. 宏常量不重视作用域

   ```c++
   void func1(){
   	const int a = 10;
   	#define A 20 
       //#undef A  //卸载宏常量A
   }
   void func2(){
   	//cout << "a:" << a << endl; //不可访问，超出了const int a作用域
   	cout << "A:" << A << endl; //#define作用域从定义到文件结束或者到#undef，可访问
   }
   int main(){
   	func2();
   	return EXIT_SUCCESS;
   }
   ```

3. 宏常量没有命名空间

   ```c++
   namespace MySpace{
   	#define num 1024
   }
   void test(){
   	//cout << MySpace::num << endl; //错误
   	//int num = 100; //命名冲突
   	cout << num << endl;
   }
   ```

   

## 七、引用(reference)

### 1、引用基本用法

在c/c++中指针的作用基本都是一样的，但是c++增加了另外一种给函数传递地址的途径，这就是按引用传递(pass-by-reference)，它也存在于其他一些编程语言中，并不是c++的发明。

- 变量名实质上是一段连续内存空间的别名，是一个标号(门牌号)
- 程序中通过变量来申请并命名内存空间
- 通过变量的名字可以使用存储空间

对一段连续的内存空间只能取一个别名吗？

答:c++新增引用后，引用可以作为一个已定义变量的别名。

### 2、基本语法

```c++
Type& ref = val
```

注意事项

- &在此不是求地址运算，而是起标识作用。
- 类型标识符是指目标变量的类型。
- 必须在声明引用变量时进行初始化。
- 引用初始化之后不能改变。
- 不能有NULL引用。必须确保引用是和一块合法的存储单元关联。
- 建立对数组的引用

```c++
void test01(){

	int a = 10;
	//给变量a取一个别名b
	int& b = a;
	cout << "a:" << a << endl;
	cout << "b:" << b << endl;
	cout << "------------" << endl;
	//操作b就相当于操作a本身
	b = 100;
	cout << "a:" << a << endl;
	cout << "b:" << b << endl;
	cout << "------------" << endl;
	//一个变量可以有n个别名
	int& c = a;
	c = 200;
	cout << "a:" << a << endl;
	cout << "b:" << b << endl;
	cout << "c:" << c << endl;
	cout << "------------" << endl;
	//a,b,c的地址都是相同的
	cout << "a:" << &a << endl;
	cout << "b:" << &b << endl;
	cout << "c:" << &c << endl;
}
```

建立数组引用

```c++
//1.建立数组引用方法一
typedef int ArrRef[10];
int arr[10];
ArrRef& aRef = arr;
for (int i = 0; i < 10;i ++){
	aRef[i] = i+1;
}
for (int i = 0; i < 10;i++){
	cout << arr[i] << " ";
}
cout << endl;
//2. 建立数组引用方法二
int(&f)[10] = arr;
for (int i = 0; i < 10; i++){
	f[i] = i+10;
}
for (int i = 0; i < 10; i++){
	cout << arr[i] << " ";
}
cout << endl;
```

### 3.引用的本质

**引用的本质在C++内部实现是一个常指针**

```c++
Type& ref = val;//Type* const ref = &val;
```

c++编译器在编译过程中使用常指针作为引用的内部实现，因此引用所占用的空间大小与指针相同，只是这个过程是编译器内部实现，用户不可见。

```c++
//发现是引用，转换为 int* const ref = &a;
void testFunc(int& ref){
	ref = 100; // ref是引用，转换为*ref = 100
}
int main(){
	int a = 10;
	int& aRef = a; //自动转换为int* const aRef = &a;这也能说明引用为什么必须初始化
	aRef = 20; //内部发现aRef是引用，自动帮我们转换为: *aRef = 20;
	cout << "a:" << a << endl;
	cout << "aRef:" << aRef << endl;
	testFunc(a);
	return EXIT_SUCCESS;
}
```

### 4.指针引用

在c语言中如果想改变一个指针的指向而不是它所指向的内容，函数声明可能这样:

```c++
void fun(int**);
```

给指针变量取一个别名。

```c++
Type* pointer = NULL;
Type* &anothername = pointer;
```

```c++
struct Teacher{
	int mAge;
};
//指针间接修改teacher的年龄
void AllocateAndInitByPointer(Teacher** teacher){
	*teacher = (Teacher*)malloc(sizeof(Teacher*));
	(*teacher)->mAge = 200;  
}
//引用修改teacher年龄
void AllocateAndInitByReference(Teacher*& teacher){
	teacher->mAge = 300;
}
void test(){
	//创建Teacher
	Teacher* teacher = NULL;
	//指针间接赋值
	AllocateAndInitByPointer(&teacher);
	cout << "AllocateAndInitByPointer:" << teacher->mAge << endl;
	//引用赋值,将teacher本身传到ChangeAgeByReference函数中
	AllocateAndInitByReference(teacher);
	cout << "AllocateAndInitByReference:" << teacher->mAge << endl;
	free(teacher);
}
```

对于c++中的定义那个，语法清晰多了。函数参数变成指针的引用，用不着取得指针的地址。

### 5.常量引用

常量引用的定义格式

```c++
const Type& ref =  val;
```

常量引用注意:

- 字面不能赋值给引用，但是可以赋值给const
- const修饰的引用，不能修改

```c++
void test01(){
	int a = 100;
	const int& aRef = a; //此时aRef就是a
	//aRef = 200; 不能通过aRef的值
	a = 100; //OK
	cout << "a:" << a << endl;
	cout << "aRef:" << aRef << endl;
}
void test02(){
	//不能把一个字面量赋给引用
	//int& ref = 100;
	//但是可以把一个字面量赋给常引用
	const int& ref = 100; //int temp = 200; const int& ret = temp;
}
```

### 6.引用使用场景

常量引用主要用在函数的形参，尤其是类的拷贝/复制构造函数。

将函数的形参定义为常量的好处：

- 引用不产生新的变量，减少形参与实参传递时的开销
- 由于引用可能导致实参随形参改变而改变，将其定义为常量引用可以消除这种副作用。

如果希望实参随着形参的改变而改变，那么使用一般的引用，如果不希望实参随着形参改变，那么使用常引用。

```c++
//const int& param防止函数中意外修改数据
void ShowVal(const int& param){
	cout << "param:" << param << endl;
}
```

**引用使用中注意点:**

最常见看见引用的地方是在**函数参数**和**返回值**中。当引用被用作函数参数的时，在函数内对任何引用的修改，将对还函数外的参数产生改变。当然，可以通过传递一个指针来做相同的事情，但引用具有更清晰的语法。

如果从函数中返回一个引用，必须像从函数中返回一个指针一样对待。当函数返回值时，引用关联的内存一定要存在。

```c++
//值传递
void ValueSwap(int m,int n){
	int temp = m;
	m = n;
	n = temp;
}
//地址传递
void PointerSwap(int* m,int* n){
	int temp = *m;
	*m = *n;
	*n = temp;
}
//引用传递
void ReferenceSwap(int& m,int& n){
	int temp = m;
	m = n;
	n = temp;
}
void test(){
	int a = 10;
	int b = 20;
	//值传递
	ValueSwap(a, b);
	cout << "a:" << a << " b:" << b << endl;
	//地址传递
	PointerSwap(&a, &b);
	cout << "a:" << a << " b:" << b << endl;
	//引用传递
	ReferenceSwap(a, b);
	cout << "a:" << a << " b:" << b << endl;
}
```

通过引用参数产生的效果同按地址传递是一样的。引用的语法更清楚简单:

1. 函数调用时传递的实参不必加&
2. 在被调函数中不必在参数前加*符

引用作为其它变量的别名而存在，因此在一些场合可以代替指针。C++主张用引用传递取代地址传递的方式，因为引用语法容易且不易出错。

```c++
//返回局部变量引用
int& TestFun01(){
	int a = 10; //局部变量
	return a;
}
//返回静态变量引用
int& TestFunc02(){	
	static int a = 20;
	cout << "static int a : " << a << endl;
	return a;
}
int main(){
	//不能返回局部变量的引用
	int& ret01 = TestFun01();
	//如果函数做左值，那么必须返回引用
	TestFunc02();
	TestFunc02() = 100;
	TestFunc02();

	return EXIT_SUCCESS;
}
```

- 调用完成局部变量销毁，不能返回局部变量的引用
- 函数当左值，必须返回引用

## 八、内联函数

### 1.内联函数的引出

在c中我们经常把一些短并且执行频繁的计算写成宏，而不是函数，这样做的理由是为了执行效率，宏可以避免函数调用的开销，这些都由预处理来完成。

在c++出现之后，使用预处理宏会出现两个问题：

1. 第一个在c中也会出现，宏看起来像一个函数调用，但是会有隐藏一些难以发现的错误。
2. 第二个问题是c++特有的，预处理器不允许访问类的成员，也就是说预处理器宏不能用作类类的成员函数。

为了保持预处理宏的效率又增加安全性，而且还能像一般成员函数那样可以在类里访问自如，c++引入了内联函数

内联函数为了**继承宏函数的效率，没有函数调用时开销，然后又可以像普通函数那样，可以进行参数，返回值类型的安全检查，又可以作为成员函数。**

### 2.预处理宏的缺陷

预处理器宏存在问题的关键是我们可能认为预处理器的行为和编译器的行为是一样的。当然也是由于宏函数调用和函数调用在外表看起来是一样的，因为也容易被混淆。

但是其中也会有一些微妙的问题出现:

1. ```c++
   #define ADD(x,y) x+y
   inline int Add(int x,int y){
   	return x + y;
   }
   void test(){
   	int ret1 = ADD(10, 20) * 10; //希望的结果是300
   	int ret2 = Add(10, 20) * 10; //希望结果也是300
   	cout << "ret1:" << ret1 << endl; //210
   	cout << "ret2:" << ret2 << endl; //300
   }
   ```

2. ```c++
   #define COMPARE(x,y) ((x) < (y) ? (x) : (y))
   int Compare(int x,int y){
   	return x < y ? x : y;
   }
   void test02(){
   	int a = 1;
   	int b = 3;
   	//cout << "COMPARE(++a, b):" << COMPARE(++a, b) << endl; // 3 展开替换a递增两次
   	cout << "Compare(int x,int y):" << Compare(++a, b) << endl; //2 递增一次传值
   }
   ```

3. 预定义宏函数没有作用域概念，无法作为一个类的成员函数，也就是说预定义宏没有办法表示类的范围。

### 3.内联函数

最好先看类和对象部分再回过头来看b与c部分

#### a.内联函数的基本概念

在c++中，预定义宏的概念是用内联函数来实现的，而**内联函数本身也是一个真正的函数**。内联函数具有普通函数的所有行为。唯一不同之处在于内联函数会在适当的地方像预定义宏一样展开，所以不需要函数调用的开销。因此应该不使用宏，使用内联函数。

**在普通函数(非成员函数)函数前面加上inline关键字使之成为内联函数。**但是必须注意必须函数体和声明结合在一起，否则编译器将它作为普通函数来对待。

```c++
inline void func(int a);
```

以上写法没有任何效果，仅仅是声明函数，应该如下方式来做:

```c++
inline int func(int a){return a++;}
```

注意: 编译器将会检查函数参数列表使用是否正确，并返回值(进行必要的转换)。这些事预处理器无法完成的。

内联函数的确占用空间，但是内联函数相对于普通函数的优势只是省去了函数调用时候的压栈，跳转，返回的开销。我们可以理解为内联函数是以**空间换时间**

#### b.类内部的内联函数

为了定义内联函数，通常必须在函数定义前面放一个inline关键字。但是在类内部定义内联函数时并不是必须的。任何在类内部定义的函数自动成为内联函数。

```c++
class Person{
public:
	Person(){ cout << "构造函数!" << endl; }
	void PrintPerson(){ cout << "输出Person!" << endl; }
}
```

构造函数Person，成员函数PrintPerson在类的内部定义，自动成为内联函数。

#### c.内联函数和编译器

内联函数并不是何时何地都有效，为了理解内联函数何时有效，应该要知道编译器碰到内联函数会怎么处理。

对于任何类型的函数，编译器会将函数类型(包括函数名字，参数类型，返回值类型)放入到符号表中。同样，当编译器看到内联函数，并且对内联函数体进行分析没有发现错误时，也会将内联函数放入符号表。

当调用一个内联函数的时候，编译器首先确保传入参数类型是正确匹配的，或者如果类型不正完全匹配，但是可以将其转换为正确类型，并且返回值在目标表达式里匹配正确类型，或者可以转换为目标类型，内联函数就会直接替换函数调用，这就消除了函数调用的开销。假如内联函数是成员函数，对象this指针也会被放入合适位置。

类型检查和类型转换、包括在合适位置放入对象this指针这些都是预处理器不能完成的。

但是c++内联编译会有一些限制，以下情况编译器可能考虑不会将函数进行内联编译：

- 不能存在任何形式的循环语句
- 不能存在过多的条件判断语句
-  函数体不能过于庞大
- 不能对函数进行取址操作

**内联仅仅只是给编译器一个建议，编译器不一定会接受这种建议，如果你没有将函数声明为内联函数，那么编译器也可能将此函数做内联编译。一个好的编译器将会内联小的、简单的函数。**

## 九、其他的函数扩展

### 1.函数的默认参数

c++在声明函数原型的时可为一个或者多个参数指定默认(缺省)的参数值，当函数调用的时候如果没有指定这个值，编译器会自动用默认值代替。

```c++
void TestFunc01(int a = 10, int b = 20){
	cout << "a + b  = " << a + b << endl;
}
//注意点:
//1. 形参b设置默认参数值，那么后面位置的形参c也需要设置默认参数
void TestFunc02(int a,int b = 10,int c = 10){}
//2. 如果函数声明和函数定义分开，函数声明设置了默认参数，函数定义不能再设置默认参数
void TestFunc03(int a = 0,int b = 0);
void TestFunc03(int a, int b){}

int main(){
	//1.如果没有传参数，那么使用默认参数
	TestFunc01();
	//2. 如果传一个参数，那么第二个参数使用默认参数
	TestFunc01(100);
	//3. 如果传入两个参数，那么两个参数都使用我们传入的参数
	TestFunc01(100, 200);

	return EXIT_SUCCESS;
}
```

注意点:

- 函数的默认参数从左向右，如果一个参数设置了默认参数，那么这个参数之后的参数都必须设置默认参数。
- 如果函数声明和函数定义分开写，函数声明和函数定义不能同时设置默认参数。

### 2.函数的占位参数

c++在声明函数时，可以设置占位参数。占位参数只有参数类型声明，而没有参数名声明。一般情况下，在函数体内部无法使用占位参数。

```c++
void TestFunc01(int a,int b,int){
	//函数内部无法使用占位参数
	cout << "a + b = " << a + b << endl;
}
//占位参数也可以设置默认值
void TestFunc02(int a, int b, int = 20){
	//函数内部依旧无法使用占位参数
	cout << "a + b = " << a + b << endl;
}
int main(){

	//错误调用，占位参数也是参数，必须传参数
	//TestFunc01(10,20); 
	//正确调用
	TestFunc01(10,20,30);
	//正确调用
	TestFunc02(10,20);
	//正确调用
	TestFunc02(10, 20, 30);

	return EXIT_SUCCESS;
}
```

什么时候用，在后面操作符重载的后置++会用到

### 3.函数重载

#### a.函数重载概述

在传统c语言中，函数名必须是唯一的，程序中不允许出现同名的函数。在c++中是允许出现同名的函数，这种现象称为函数重载。

函数重载的目的就是为了方便的使用函数名。

#### b.函数重载的基本语法

实现函数重载的条件：

- 同一个作用域
- 参数个数不同
- 参数类型不同
- 参数顺序不同

```c++
//1. 函数重载条件
namespace A{
	void MyFunc(){ cout << "无参数!" << endl; }
	void MyFunc(int a){ cout << "a: " << a << endl; }
	void MyFunc(string b){ cout << "b: " << b << endl; }
	void MyFunc(int a, string b){ cout << "a: " << a << " b:" << b << endl;}
    void MyFunc(string b, int a){cout << "a: " << a << " b:" << b << endl;}
}
//2.返回值不作为函数重载依据
namespace B{
	void MyFunc(string b, int a){}
	//int MyFunc(string b, int a){} //无法重载仅按返回值区分的函数
}
```

注意：函数重载和默认参数一起使用，但要注意二义性产生

#### c.函数重载实现原理

编译器为了实现函数重载，也是默认为我们做了一些幕后的工作，编译器用不同的参数类型来修饰不同的函数名，比如void func(); 编译器可能会将函数名修饰成_func，当编译器碰到void func(int x),编译器可能将函数名修饰为func_int,当编译器碰到void func(int x,char c),编译器可能会将函数名修饰为_func_int_char我这里使用”可能”这个字眼是因为编译器如何修饰重载的函数名称并没有一个统一的标准，所以不同的编译器可能会产生不同的内部名。

```c++
void func(){}
void func(int x){}
void func(int x,char y){}
```

以上三个函数在linux下生成的编译之后的函数名为:

```c++
_Z4funcv //v 代表void,无参数
_Z4funci //i 代表参数为int类型
_Z4funcic //i 代表第一个参数为int类型，第二个参数为char类型
```

### 3、extern "C"浅析

在linux中测试

```c++
c函数: void MyFunc(){} ,被编译成函数: MyFunc
c++函数: void MyFunc(){},被编译成函数: _Z6Myfuncv
```

通过这个测试，由于c++中需要支持函数重载，所以c和c++中对同一个函数经过编译后生成的函数名是不相同的，这就导致了一个问题，如果在c++中调用一个使用c语言编写模块中的某个函数，那么c++是根据c++的名称修饰方式来查找并链接这个函数，那么就会发生链接错误，以上例，c++中调用MyFunc函数，在链接阶段会去找Z6Myfuncv，结果是没有找到的，因为这个MyFunc函数是c语言编写的，生成的符号是MyFunc

那么如果想在c++调用c的函数怎么办？

extern "C"的主要作用就是为了实现c++代码能够调用其他c语言代码。加上extern "C"后，这部分代码编译器按c语言的方式进行**编译**和**链接**，而不是按c++的方式。

**MyModule.h**

```c++
#ifndef MYMODULE_H
#define MYMODULE_H

#include<stdio.h>

#ifdef __cplusplus
extern "C"{
#endif

	void func1();
	int func2(int a,int b);

#ifdef __cplusplus
}
#endif

#endif
```

**MyModule.c**

```c++
#include"MyModule.h"

void func1(){
	printf("hello world!");
}
int func2(int a, int b){
	return a + b;
}
```

**TestExternC.cpp**

```c++
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

#if 0

	#ifdef __cplusplus
	extern "C" {
		#if 0
			void func1();
			int func2(int a, int b);
		#else
			#include"MyModule.h"
		#endif
	}

	#endif

#else

	extern "C" void func1();
	extern "C" int func2(int a, int b);

#endif

int main(){
	func1();
	cout << func2(10, 20) << endl;
	return EXIT_SUCCESS;
}
```

## 十、类和对象初探

### 1.类和对象的基本概念

#### a.C和C++中struct区别

- c语言struct只有变量
- c++语言struct既有变量，也有函数

#### b.类的封装

在C中要表示对象

```c++
typedef struct _Person{
	char name[64];
	int age;
}Person;
typedef struct _Aninal{
	char name[64];
	int age;
	int type; //动物种类
}Ainmal;

void PersonEat(Person* person){
	printf("%s在吃人吃的饭!\n",person->name);
}
void AnimalEat(Ainmal* animal){
	printf("%s在吃动物吃的饭!\n", animal->name);
}

int main(){

	Person person;
	strcpy(person.name, "小明");
	person.age = 30;
	AnimalEat(&person);

	return EXIT_SUCCESS;
}
```

**在c语言中，行为和属性是分开的**，也就是说吃饭这个属性不属于某类对象，而属于所有的共同的数据，所以不单单是PeopleEat可以调用Person数据，AnimalEat也可以调用Person数据，那么万一调用错误，将会导致问题发生。

假如某对象的某项属性不想被外界获知，或者某些行为不想让外界知道，只需要自己知道就可以。那么这种情况下，封装应该再提供一种机制能够给属性和行为的访问权限控制住。

封装特性包含两个方面，一个是属性和变量合成一个整体，一个是给属性和函数增加访问权限。

- 封装
  1. 把变量(属性)和函数(操作)合成一个整体，封装在一个类中
  2. 对变量进行访问控制

- 访问权限
  1. 在类的内部(作用域范围内)，没有访问权限之分，所有成员可以互相访问
  2. 在类的外部(作用域范围外)，访问权限才有意义: public，private，protected
  3. 在类的外部，只有public修饰的成员才能被访问，在没有涉及继承与派生时，private和protected是同等级的，外部不允许访问

| 访问属性  | 属性 | 对象内部 | 对象外部 |
| --------- | ---- | -------- | -------- |
| public    | 公有 | 可访问   | 可访问   |
| protected | 保护 | 可访问   | 不可访问 |
| private   | 私有 | 可访问   | 不可访问 |

```c++
//封装两层含义
//1. 属性和行为合成一个整体
//2. 访问控制，现实事物本身有些属性和行为是不对外开放
class Person{
//人具有的行为(函数)
public:
	void Dese(){ cout << "我有钱，年轻，个子又高，就爱嘚瑟!" << endl;}
//人的属性(变量)
public:
	int mTall; //多高，可以让外人知道
protected:
	int mMoney; // 有多少钱,只能儿子孙子知道
private:
	int mAge; //年龄，不想让外人知道
};
int main(){
	Person p;
	p.mTall = 220;
	//p.mMoney 保护成员外部无法访问
	//p.mAge 私有成员外部无法访问
	p.Dese();

	return EXIT_SUCCESS;
}
```

struct与class的区别:class默认访问权限为private，struct默认访问权限为public

```c++
class A{
	int mAge;
};
struct B{
	int mAge;
};

void test(){
	A a;
	B b;
	//a.mAge; //无法访问私有成员
	b.mAge; //可正常外部访问
}
```

#### c.将成员变量设置为private

1. 可赋予客户端访问数据的一致性。

   如果成员变量不是public，客户端唯一能够访问对象的方法就是通过成员函数。如果类中所有public权限的成员都是函数，客户在访问类成员时只会默认访问函数，不需要考虑访问的成员需不需要添加(),这就省下了许多搔首弄耳的时间。

2. 可细微划分访问控制

   使用成员函数可使得我们对变量的控制处理更加精细。如果我们让所有的成员变量为public，每个人都可以读写它。如果我们设置为private，我们可以实现“不准访问”、“只读访问”、“读写访问”，甚至你可以写出“只写访问”。

   ```c++
   class AccessLevels{
       public:
           //对只读属性进行只读访问
           int getReadOnly(){ return readOnly; }
           //对读写属性进行读写访问
           void setReadWrite(int val){ readWrite = val; }
           int getReadWrite(){ return readWrite; }
           //对只写属性进行只写访问
           void setWriteOnly(int val){ writeOnly = val; }
       private:
           int readOnly; //对外只读访问
           int noAccess; //外部不可访问
           int readWrite; //读写访问
           int writeOnly; //只写访问
   };
   ```

### 2.面向对象程序设计实例

#### a.设计立方体类

设计立方体类(Cube)，求出立方体的面积( 2*a*b + 2*a*c + 2*b*c )和体积( a * b * c)，分别用**全局函数**和**成员函数**判断两个立方体是否相等。

```c++
//立方体类
class Cub{
public:
	void setL(int l){ mL = l; }
	void setW(int w){ mW = w; }
	void setH(int h){ mH = h; }
	int getL(){ return mL; }
	int getW(){ return mW; }
	int getH(){ return mH; }
	//立方体面积
	int caculateS(){ return (mL*mW + mL*mH + mW*mH) * 2; }
	//立方体体积
	int caculateV(){ return mL * mW * mH; }
	//成员方法
	bool CubCompare(Cub& c){
		if (getL() == c.getL() && getW() == c.getW() && getH() == c.getH()){
			return true;
		}
		return false;
	}
private:
	int mL; //长
	int mW; //宽
	int mH; //高
};

//比较两个立方体是否相等
bool CubCompare(Cub& c1, Cub& c2){
	if (c1.getL() == c2.getL() && c1.getW() == c2.getW() && c1.getH() == c2.getH()){
		return true;
	}
	return false;
}

void test(){
	Cub c1, c2;
	c1.setL(10);
	c1.setW(20);
//立方体类
class Cub{
public:
	void setL(int l){ mL = l; }
	void setW(int w){ mW = w; }
	void setH(int h){ mH = h; }
	int getL(){ return mL; }
	int getW(){ return mW; }
	int getH(){ return mH; }
	//立方体面积
	int caculateS(){ return (mL*mW + mL*mH + mW*mH) * 2; }
	//立方体体积
	int caculateV(){ return mL * mW * mH; }
	//成员方法
	bool CubCompare(Cub& c){
		if (getL() == c.getL() && getW() == c.getW() && getH() == c.getH()){
			return true;
		}
		return false;
	}
private:
	int mL; //长
	int mW; //宽
	int mH; //高
};

//比较两个立方体是否相等
bool CubCompare(Cub& c1, Cub& c2){
	if (c1.getL() == c2.getL() && c1.getW() == c2.getW() && c1.getH() == c2.getH()){
		return true;
	}
	return false;
}

void test(){
	Cub c1, c2;
	c1.setL(10);
	c1.setW(20);
	c1.setH(30);

	c2.setL(20);
	c2.setW(20);
	c2.setH(30);

	cout << "c1面积:" << c1.caculateS() << " 体积:" << c1.caculateV() << endl;
	cout << "c2面积:" << c2.caculateS() << " 体积:" << c2.caculateV() << endl;

	//比较两个立方体是否相等
	if (CubCompare(c1, c2)){
		cout << "c1和c2相等!" << endl;
	}
	else{
		cout << "c1和c2不相等!" << endl;
	}

	if (c1.CubCompare(c2)){
		cout << "c1和c2相等!" << endl;
	}
	else{
		cout << "c1和c2不相等!" << endl;
	}
}
```

#### b.点和圆的关系

设计一个圆形类（AdvCircle），和一个点类（Point），计算点和圆的关系。假如圆心坐标为x0, y0, 半径为r，点的坐标为x1, y1：

1.点在圆上 2.点在圆内 3.点在圆外

```c++
//点类
class Point{
public:
	void setX(int x){ mX = x; }
	void setY(int y){ mY = y; }
	int getX(){ return mX; }
	int getY(){ return mY; }
private:
	int mX;
	int mY;
};

//圆类
class Circle{
public:
	void setP(int x,int y){
		mP.setX(x);
		mP.setY(y);
	}
	void setR(int r){ mR = r; }
	Point& getP(){ return mP; }
	int getR(){ return mR; }
	//判断点和圆的关系
	void IsPointInCircle(Point& point){
		int distance = (point.getX() - mP.getX()) * (point.getX() - mP.getX()) + (point.getY() - mP.getY()) * (point.getY() - mP.getY());
		int radius = mR * mR;
		if (distance < radius){
			cout << "Point(" << point.getX() << "," << point.getY() << ")在圆内!" << endl;
		}
		else if (distance > radius){
			cout << "Point(" << point.getX() << "," << point.getY() << ")在圆外!" << endl;
		}
		else{
			cout << "Point(" << point.getX() << "," << point.getY() << ")在圆上!" << endl;
		}
	}
private:
	Point mP; //圆心
	int mR; //半径
};

void test(){
	//实例化圆对象
	Circle circle;
	circle.setP(20, 20);
	circle.setR(5);
	//实例化点对象
	Point point;
	point.setX(25);
	point.setY(20);

	circle.IsPointInCircle(point);
}
```

#### c.分文件实现

将上述函数和类分别用头文件和源文件实现

### 3.对象的构造和析构

#### a.初始化和清理

构造函数主要作用在于创建对象时为对象的成员属性赋值，构造函数由编译器自动调用，无须手动调用。

析构函数主要用于对象**销毁前**系统自动调用，执行一些清理工作。

- 构造函数语法
  1. 构造函数名和类名相同，没有返回值，不能有void，但可以有参数
  2. ClassName(){}

- 析构函数语法
  1. 析构函数名是在类名前面加"~"组成，没有返回值，不能有参数，不能重载
  2. ~ClassName(){}

```c++
class Person{
public:
	Person(){
		cout << "构造函数调用!" << endl;
		pName = (char*)malloc(sizeof("John"));
		strcpy(pName, "John");
		mTall = 150;
		mMoney = 100;
	}
	~Person(){
		cout << "析构函数调用!" << endl;
		if (pName != NULL){
			free(pName);
			pName = NULL;
		}
	}
public:
	char* pName;
	int mTall;
	int mMoney;
};

void test(){
	Person person;
	cout << person.pName << person.mTall << person.mMoney << endl;
}
```

#### b.构造函数的分类及调用

- 按参数类型:分为无参构造函数和有参构造函数
- 按类型分类:普通构造函数和拷贝构造函数(复制构造函数)

```c++
class Person{
public:
	Person(){
		cout << "no param constructor!" << endl;
		mAge = 0;
	}
	//有参构造函数
	Person(int age){
		cout << "1 param constructor!" << endl;
		mAge = age;
	}
	//拷贝构造函数(复制构造函数) 使用另一个对象初始化本对象
	Person(const Person& person){
		cout << "copy constructor!" << endl;
		mAge = person.mAge;
	}
	//打印年龄
	void PrintPerson(){
		cout << "Age:" << mAge << endl;
	}
private:
	int mAge;
};
//1. 无参构造调用方式
void test01(){
	
	//调用无参构造函数
	Person person1; 
	person1.PrintPerson();

	//无参构造函数错误调用方式
	//Person person2();
	//person2.PrintPerson();
}
//2. 调用有参构造函数
void test02(){
	
	//第一种 括号法，最常用
	Person person01(100);
	person01.PrintPerson();

	//调用拷贝构造函数
	Person person02(person01);
	person02.PrintPerson();

	//第二种 匿名对象(显示调用构造函数)
	Person(200); //匿名对象，没有名字的对象

	Person person03 = Person(300);
	person03.PrintPerson();

	//注意: 使用匿名对象初始化判断调用哪一个构造函数，要看匿名对象的参数类型
	Person person06(Person(400)); //等价于 Person person06 = Person(400);
	person06.PrintPerson();

	//第三种 =号法 隐式转换
	Person person04 = 100; //Person person04 =  Person(100)
	person04.PrintPerson();

	//调用拷贝构造
	Person person05 = person04; //Person person05 =  Person(person04)
	person05.PrintPerson();
}
```

**b为A的实例化对象,A a = A(b) 和 A(b)的区别？**

当A(b) 有变量来接的时候，那么编译器认为他是一个匿名对象，当没有变量来接的时候，编译器认为你A(b) 等价于 A b.

```c++
class Teacher{
public:
	Teacher(){
		cout << "默认构造函数!" << endl;
	}
	Teacher(const Teacher& teacher){
		cout << "拷贝构造函数!" << endl;
	}
public:
	int mAge;
};
void test(){
	
	Teacher t1;
	//error C2086:“Teacher t1”: 重定义
	Teacher(t1);  //此时等价于 Teacher t1;
}
```

#### c.拷贝构造函数调用时机

- 对象以值传递的方式传给函数参数
- 函数局部对象以值传递的方式从函数返回
- 用一个对象初始化另一个对象

```c++
class Person{
public:
	Person(){
		cout << "no param contructor!" << endl;
		mAge = 10;
	}
	Person(int age){
		cout << "param constructor!" << endl;
		mAge = age;
	}
	Person(const Person& person){
		cout << "copy constructor!" << endl;
		mAge = person.mAge;
	}
	~Person(){
		cout << "destructor!" << endl;
	}
public:
	int mAge;
};
//1. 旧对象初始化新对象
void test01(){
	Person p(10);
	Person p1(p);
	Person p2 = Person(p);
	Person p3 = p; // 相当于Person p2 = Person(p);
}
//2. 传递的参数是普通对象，函数参数也是普通对象，传递将会调用拷贝构造
void doBussiness(Person p){}

void test02(){
	Person p(10);
	doBussiness(p);
}
//3. 函数返回局部对象
Person MyBusiness(){
	Person p(10);
	cout << "局部p:" << (int*)&p << endl;
	return p;
}
void test03(){
	//vs release、qt下没有调用拷贝构造函数
	//vs debug下调用一次拷贝构造函数
	Person p = MyBusiness();
	cout << "局部p:" << (int*)&p << endl;
}
```

#### d.构造函数调用规则

- 默认情况下，c++编译器至少为我们写的类增加三个函数
  1. 默认构造函数(无参，函数体为空)
  2. 默认析构函数(无参，函数体为空)
  3. 默认拷贝构造函数，对类中非静态成员属性简单值拷贝
- 如果用户定义拷贝构造函数，c++不会再提供任何默认构造函数
- 如果用户定义了普通构造函数(非拷贝)，c++不在提供默认无参构造，但是会提供默认拷贝构造

### 4.深拷贝和浅拷贝

#### a.浅拷贝

同一类型的对象之间可以赋值，使得两个对象的成员变量的值相同，两个对象仍然是独立的两个对象，这种情况被称为**浅拷贝.**

一般情况下，浅拷贝没有任何副作用，但是当类中有指针，并且指针指向动态分配的内存空间，析构函数做了动态内存释放的处理，会导致内存问题。

![](C++%20is%20an%20extension%20of%20C.assets/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-07-19%20141255.png)

#### b.深拷贝

当类中有指针，并且此指针有动态分配空间，析构函数做了释放处理，往往需要自定义拷贝构造函数，自行给指针动态分配空间，深拷贝。

![](C++%20is%20an%20extension%20of%20C.assets/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-07-19%20141716.png)

```c++
class Person{
public:
	Person(char* name,int age){
		pName = (char*)malloc(strlen(name) + 1);
		strcpy(pName,name);
		mAge = age;
	}
	//增加拷贝构造函数
	Person(const Person& person){
		pName = (char*)malloc(strlen(person.pName) + 1);
		strcpy(pName, person.pName);
		mAge = person.mAge;
	}
	~Person(){
		if (pName != NULL){
			free(pName);
		}
	}
private:
	char* pName;
	int mAge;
};

void test(){
	Person p1("Edward",30);
	//用对象p1初始化对象p2,调用c++提供的默认拷贝构造函数
	Person p2 = p1;
}
```

### 5.多个对象构造和析构

#### a.初始化列表

构造函数和其他函数不同，除了有名字，参数列表，函数体之外还有初始化列表。

初始化列表简单使用；

```c++
class Person{
public:
#if 0
	//传统方式初始化
	Person(int a,int b,int c){
		mA = a;
		mB = b;
		mC = c;
	}
#endif
	//初始化列表方式初始化
	Person(int a, int b, int c):mA(a),mB(b),mC(c){}
	void PrintPerson(){
		cout << "mA:" << mA << endl;
		cout << "mB:" << mB << endl;
		cout << "mC:" << mC << endl;
	}
private:
	int mA;
	int mB;
	int mC;
};
```

**注意:**初始化成员列表(参数列表)只能在构造函数使用

#### b.类对象作为成员

在类中定义的数据成员一般都是基本的数据类型。但是类中的成员也可以是对象，叫做**对象成员**。

C++中对对象的初始化是非常重要的操作，当创建一个对象的时候，c++编译器必须确保调用了所有子对象的构造函数。如果所有的子对象有默认构造函数，编译器可以自动调用他们。但是如果子对象没有默认的构造函数，或者想指定调用某个构造函数怎么办？

那么是否可以在类的构造函数直接调用子类的属性完成初始化呢？但是如果子类的成员属性是私有的，我们是没有办法访问并完成初始化的。

解决办法非常简单：对于子类调用构造函数，c++为此提供了专门的语法，即构造函数初始化列表。

当调用构造函数时，**首先按各对象成员在类定义中的顺序（和参数列表的顺序无关）依次调用它们的构造函数，对这些对象初始化，最后再调用本身的函数体。也就是说，先调用对象成员的构造函数，再调用本身的构造函数。**

**析构函数和构造函数调用顺序相反，先构造，后析构。**

```c++
//汽车类
class Car{
public:
	Car(){
		cout << "Car 默认构造函数!" << endl;
		mName = "大众汽车";
	}
	Car(string name){
		cout << "Car 带参数构造函数!" << endl;
		mName = name;
	}
	~Car(){
		cout << "Car 析构函数!" << endl;
	}
public:
	string mName;
};

//拖拉机
class Tractor{
public:
	Tractor(){
		cout << "Tractor 默认构造函数!" << endl;
		mName = "爬土坡专用拖拉机";
	}
	Tractor(string name){
		cout << "Tractor 带参数构造函数!" << endl;
		mName = name;
	}
	~Tractor(){
		cout << "Tractor 析构函数!" << endl;
	}
public:
	string mName;
};
//人类
class Person{
public:
#if 1
	//类mCar不存在合适的构造函数
	Person(string name){
		mName = name;
	}
#else
	//初始化列表可以指定调用构造函数
	Person(string carName, string tracName, string name) : mTractor(tracName), mCar(carName), mName(name){
		cout << "Person 构造函数!" << endl;
	}
#endif
	
	void GoWorkByCar(){
		cout << mName << "开着" << mCar.mName << "去上班!" << endl;
	}
	void GoWorkByTractor(){
		cout << mName << "开着" << mTractor.mName << "去上班!" << endl;
	}
	~Person(){
		cout << "Person 析构函数!" << endl;
	}
private:
	string mName;
	Car mCar; //编译只能调用无参的构造
	Tractor mTractor;
};

void test(){
	//Person person("宝马", "东风拖拉机", "赵四");
	Person person("刘能");
	person.GoWorkByCar();
	person.GoWorkByTractor();
}
```

### 6.explicit关键字

c++提供了关键字explicit，禁止通过构造函数进行的隐式转换。声明为explicit的构造函数不能在隐式转换中使用。

**explicit注意**

- explicit用于修饰构造函数，防止隐式转化。
- 是针对单参数的构造函数(或者除了第一个参数外其余参数都有默认值的多参构造)而言。

```c++
class MyString{
public:
	explicit MyString(int n){
		cout << "MyString(int n)!" << endl;
	}
	MyString(const char* str){
		cout << "MyString(const char* str)" << endl;
	}
};

int main(){

	//给字符串赋值？还是初始化？
	//MyString str1 = 1; 
	MyString str2(10);

	//寓意非常明确，给字符串赋值
	MyString str3 = "abcd";
	MyString str4("abcd");

	return EXIT_SUCCESS;
}
```

### 7.动态对象创建

当我们创建数组的时候，总是需要提前预定数组的长度，然后编译器分配预定长度的数组空间，在使用数组的时，会有这样的问题，数组也许空间太大了，浪费空间，也许空间不足，所以对于数组来讲，如果能根据需要来分配空间大小再好不过。

所以动态的意思意味着不确定性。

为了解决这个普遍的编程问题，在运行中可以创建和销毁对象是最基本的要求。当然c早就提供了动态内存分配（dynamic memory allocation）,函数malloc和free可以在运行时从堆中分配存储单元。

然而这些函数在c++中不能很好的运行，因为它不能帮我们完成对象的初始化工作。

#### a.C动态分配内存方法

为了在运行时动态分配内存，c在他的标准库中提供了一些函数,malloc以及它的变种calloc和realloc,释放内存的free,这些函数是有效的、但是原始的，需要程序员理解和小心使用。为了使用c的动态内存分配函数在堆上创建一个类的实例，我们必须这样做:

```c++
class Person{
public:
    Person(){
        mAge = 20;
        pName = (char*)malloc(strlen("john")+1);
        strcpy(pName, "john");
    }
    	void Init(){
		mAge = 20;
		pName = (char*)malloc(strlen("john")+1);
		strcpy(pName, "john");
	}
	void Clean(){
		if (pName != NULL){
			free(pName);
		}
	}
public:
	int mAge;
	char* pName;
};
int main(){

	//分配内存
	Person* person = (Person*)malloc(sizeof(Person));
	if(person == NULL){
		return 0;
	}
	//调用初始化函数
	person->Init();
	//清理对象
	person->Clean();
	//释放person对象
	free(person);

	return EXIT_SUCCESS;
}
```

**问题:**

- 程序员必须确定对象的长度
- malloc返回一个void *指针，C++不允许将void *
- malloc可能申请内存失败，所以必须判断返回值来确保内存分配成功
- 用户在使用对象之前必须记住对他初始化，构造函数不能显示调用初始化(构造函数是由编译器调用)，用户有可能忘记调用初始化函数。

C的动态内存分配函数太复杂，容易令人混淆，是不可接受的，C++中我们推荐使用运算符new和delete

#### b.new operator

C++中解决动态内存分配的方案是把创建一个对象所需要的操作都结合在一个称为new的运算符里。当用new创建一个对象时，它就在堆里为对象分配内存并调用构造函数完成初始化。

```c++
Person* person = new Person;
相当于:
Person* person = (Person*)malloc(sizeof(Person));
	if(person == NULL){
		return 0;
	}
person->Init();
```

New操作符能确定在调用构造函数初始化之前内存分配是成功的，所有不用显式确定调用是否成功。

现在我们发现在堆里创建对象的过程变得简单了，只需要一个简单的表达式，它带有内置的长度计算、类型转换和安全检查。这样在堆创建一个对象和在栈里创建对象一样简单。

#### c.delete operator

new表达式的反面是delete表达式。delete表达式先调用析构函数，然后释放内存。

正如new表达式返回一个指向对象的指针一样，delete需要一个对象的地址。

delete只适用于由new创建的对象。

如果使用一个由malloc或者calloc或者realloc创建的对象使用delete,这个行为是未定义的。因为大多数new和delete的实现机制都使用了malloc和free,所以很可能没有调用析构函数就释放了内存。

如果正在删除的对象的指针是NULL,将不发生任何事，因此建议在删除指针后，立即把指针赋值为NULL，以免对它删除两次，对一些对象删除两次可能会产生某些问题。

```c++
class Person{
public:
	Person(){
		cout << "无参构造函数!" << endl;
		pName = (char*)malloc(strlen("undefined") + 1);
		strcpy(pName, "undefined");
		mAge = 0;
	}
	Person(char* name, int age){
		cout << "有参构造函数!" << endl;
		pName = (char*)malloc(strlen(name) + 1);
		strcpy(pName, name);
		mAge = age;
	}
	void ShowPerson(){
		cout << "Name:" << pName << " Age:" << mAge << endl;
	}
	~Person(){
		cout << "析构函数!" << endl;
		if (pName != NULL){
			delete pName;
			pName = NULL;
		}
	}
public:
	char* pName;
	int mAge;
};

void test(){
	Person* person1 = new Person;
	Person* person2 = new Person("John",33);

	person1->ShowPerson();
	person2->ShowPerson();

	delete person1;
	delete person2;
}
```

#### d.用于数组的new和delete

使用new和delete在堆上创建数组非常容易。

```c++
//创建字符数组
char* pStr = new char[100];
//创建整型数组
int* pArr1 = new int[100]; 
//创建整型数组并初始化
int* pArr2 = new int[10]{ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

//释放数组内存
delete[] pStr;
delete[] pArr1;
delete[] pArr2;
```

**当创建一个对象数组的时候，必须对数组中的每个对象调用构造函数，**除了在栈上可以聚合初始化，必须提供默认的构造函数。

```c++
class Person{
public:
	Person(){
		pName = (char*)malloc(strlen("undefined") + 1);
		strcpy(pName, "undefined");
		mAge = 0;
	}
	Person(char* name, int age){
		pName = (char*)malloc(sizeof(name));
		strcpy(pName, name);
		mAge = age;
	}
	~Person(){
		if (pName != NULL){
			delete pName;
		}
	}
public:
	char* pName;
	int mAge;
};
void test(){
	//栈聚合初始化
	Person person[] = { Person("john", 20), Person("Smith", 22) };
	cout << person[1].pName << endl;
    //创建堆上对象数组必须提供构造函数
	Person* workers = new Person[20]; //自动调用20次无参构造函数
}
```

#### e.delete void* 可能会出错

如果对一个void*指针执行delete操作，这将可能成为一个程序错误，除非指针指向的内容是非常简单的，因为它将不执行析构函数.以下代码未调用析构函数，导致可用内存减少。

```c++
class Person{
public:
	Person(char* name, int age){
		pName = (char*)malloc(sizeof(name));
		strcpy(pName,name);
		mAge = age;
	}
	~Person(){
		if (pName != NULL){
			delete pName;
		}
	}
public:
	char* pName;
	int mAge;
};

void test(){
	void* person = new Person("john",20);
	delete person;
}
```

**问题:**malloc、free和new、delete可以混搭使用吗？也就是说malloc分配的内存，可以调用delete吗？通过new创建的对象，可以调用free来释放吗？

#### f.使用new和delete采用相同形式

```c++
Person* person = new Person[10];
delete person;
```

使用了new也搭配使用了delete，问题在于Person有10个对象，那么其他9个对象可能没有调用析构函数，也就是说其他9个对象可能删除不完全，因为它们的析构函数没有被调用。

**我们现在清楚使用new的时候发生了两件事: 一、分配内存；二、调用构造函数，那么调用delete的时候也有两件事：一、析构函数；二、释放内存。**

那么刚才我们那段代码最大的问题在于：person指针指向的内存中到底有多少个对象，因为这个决定应该有多少个析构函数应该被调用。换句话说，person指针指向的是一个单一的对象还是一个数组对象，由于单一对象和数组对象的内存布局是不同的。更明确的说，数组所用的内存通常还包括“数组大小记录”，使得delete的时候知道应该调用几次析构函数。单一对象的话就没有这个记录。单一对象和数组对象的内存布局可理解为下图:

![2016-07-18_040751](C++%20is%20an%20extension%20of%20C.assets/clip_image002.jpg)

当我们使用一个delete的时候，我们必须让delete知道指针指向的内存空间中是否存在一个“数组大小记录”的办法就是我们告诉它。当我们使用delete[]，那么delete就知道是一个对象数组，从而清楚应该调用几次析构函数。

**结论:**如果在new表达式中使用[]，必须在相应的delete表达式中也使用[].如果在new表达式中不使用[], 一定不要在相应的delete[]表达式中使用[].

### 8.静态成员函数

在类定义中，它的成员（包括成员变量和成员函数），这些成员可以用关键字static声明为静态的，称为静态成员。

不管这个类创建了多少个对象，静态成员只有一个拷贝，这个拷贝被所有属于这个类的对象共享。

#### a.静态成员变量

在一个类中，若将一个成员变量声明为static，这种成员称为静态成员变量。与一般的数据成员不同，无论建立了多少个对象，都只有一个静态数据的拷贝。静态成员变量，属于某个类，所有对象共享。

静态变量，是在编译阶段就分配空间，对象还没有创建时，就已经分配空间。

- **静态成员变量必须在类中声明，在类外定义。**
- 静态数据成员不属于某个对象，在为对象分配空间中不包括静态成员所占空间。
- 静态数据成员可以通过类名或者对象名来引用

```c++
class Person{
public:
	//类的静态成员属性
	static int sNum;
private:
	static int sOther;
};

//类外初始化，初始化时不加static
int Person::sNum = 0;
int Person::sOther = 0;
int main(){

	//1. 通过类名直接访问
	Person::sNum = 100;
	cout << "Person::sNum:" << Person::sNum << endl;

	//2. 通过对象访问
	Person p1, p2;
	p1.sNum = 200;

	cout << "p1.sNum:" << p1.sNum << endl;
	cout << "p2.sNum:" << p2.sNum << endl;

	//3. 静态成员也有访问权限，类外不能访问私有成员
	//cout << "Person::sOther:" << Person::sOther << endl;
	Person p3;
	//cout << "p3.sOther:" << p3.sOther << endl;

	system("pause");
	return EXIT_SUCCESS;
}
```

#### b.静态成员函数

在类定义中，前面有static说明的成员函数称为静态成员函数。静态成员函数使用方式和静态变量一样，同样在对象没有创建前，即可通过类名调用。**静态成员函数主要为了访问静态变量，但是，不能访问普通成员变量。**

静态成员函数的意义，不在于信息共享，数据沟通，而在于管理静态数据成员，完成对静态数据成员的封装。

- 静态成员函数只能访问静态变量，不能访问普通成员变量。
- 静态成员函数的使用和静态成员变量一样。
- 静态成员函数也有访问权限。
- 普通成员函数可以访问静态成员变量、也可以访问非静态成员变量。

```c++
class Person{
public:
	//普通成员函数可以访问static和non-static成员属性
	void changeParam1(int param){
		mParam = param;
		sNum = param;
	}
	//静态成员函数只能访问static成员属性
	static void changeParam2(int param){
		//mParam = param; //无法访问
		sNum = param;
	}
private:
	static void changeParam3(int param){
		//mParam = param; //无法访问
		sNum = param;
	}
public:
	int mParam;
	static int sNum;
};

//静态成员属性类外初始化
int Person::sNum = 0;

int main(){

	//1. 类名直接调用
	Person::changeParam2(100);

	//2. 通过对象调用
	Person p;
	p.changeParam2(200);

	//3. 静态成员函数也有访问权限
	//Person::changeParam3(100); //类外无法访问私有静态成员函数
	//Person p1;
	//p1.changeParam3(200);
	return EXIT_SUCCESS;
}
```

#### c.const静态成员属性

如果一个类的成员，既要实现共享，又要实现不可改变，那就用 static const 修饰。**定义静态const数据成员时，最好在类内部初始化**。

```c++
class Person{
public:
	//static const int mShare = 10;
	const static int mShare = 10; //只读区
};
int main(){

	cout << Person::mShare << endl;
	//Person::mShare = 20;

	return EXIT_SUCCESS;
}
```

#### d.单例模式

单例模式是一种常用的软件设计模式。在它的核心结构中只包含一个被称为单例的特殊类。通过单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约系统资源。如果希望在系统中某个类的对象只能存在一个，单例模式是最好的解决方案。

![IMG_256](C++%20is%20an%20extension%20of%20C.assets/clip_image002-1721462367105.jpg)

Singleton（单例）：在单例类的内部实现只生成一个实例，同时它提供一个静态的getInstance()工厂方法，让客户可以访问它的唯一实例；为了防止在外部对其实例化，将其默认构造函数和拷贝构造函数设计为私有；在单例类内部定义了一个Singleton类型的静态对象，作为外部共享的唯一实例。

例如:用单例模式，模拟公司员工使用打印机场景，打印机可以打印员工要输出的内容，并且可以累积打印机使用次数。

```c++
#include <iostream>
using namespace std;

class Printer {
public:
    static Printer* getInstance() {
        return &pPrinter;
    }
    void PrintText(string text) {
        cout << "打印内容: " << text << endl;
        cout << "已打印次数: " << mTimes << endl;
        cout << "--------------" << endl;
        mTimes++;
    }
private:
    Printer() {
        mTimes = 0;
    }
    Printer(const Printer&) = delete;
    Printer& operator=(const Printer&) = delete;
private:
    static Printer* pPrinter;
    int mTimes;
};

Printer* Printer::pPrinter = new Printer();

void test() {
    Printer* printer = Printer::getInstance();
    printer->PrintText("离职报告!");
    printer->PrintText("入职合同!");
    printer->PrintText("提交代码!");
}

int main() {
    test();
    return 0;
}
```

## 十一、面向对象模型(类和对象深探)

### 1.成员变量和成员函数的存储

c++实现了“封装”，那么数据(成员属性)和操作(成员函数)是什么样的呢？

“数据”和“处理数据的操作(函数)”是分开存储的。

- c++中的**非静态数据成员**直接内含在类对象中，就像c struct一样。
- 成员函数(member function)虽然内含在class声明之内，却不出现在对象中。
- 每一个非内联函数(non-inline member function)只会诞生一份函数实例。

```c++
#include <iostream>
using namespace std;

class MyClass01{
public:
	int mA;
};

class MyClass02{
public:
	int mA;
	static int sB;
};

class MyClass03{
public:
	void printMyClass(){
		cout << "hello world!" << endl;
	}
public:
	int mA;
	static int sB;
};

class MyClass04{
public:
	void printMyClass(){
		cout << "hello world!" << endl;
	}
	static void ShowMyClass(){
		cout << "hello world！" << endl;
	}
public:
	int mA;
	static int sB;
};

int main(){

	MyClass01 mclass01;
	MyClass02 mclass02;
	MyClass03 mclass03;
	MyClass04 mclass04;

	cout << "MyClass01:" << sizeof(mclass01) << endl; //4
	//静态数据成员并不保存在类对象中
	cout << "MyClass02:" << sizeof(mclass02) << endl; //4
	//非静态成员函数不保存在类对象中
	cout << "MyClass03:" << sizeof(mclass03) << endl; //4
	//静态成员函数也不保存在类对象中
	cout << "MyClass04:" << sizeof(mclass04) << endl; //4

	return EXIT_SUCCESS;
}
```

**上面的实例可以看出C++类对象中的变量和函数分开存储。**

### 2.this指针

#### a.this指针工作原理

c++的数据和操作也是分开存储，并且每一个非内联成员函数(non-inline member function)只会诞生一份函数实例，也就是说多个同类型的对象会共用一块代码。

那么问题是：这一块代码是如何区分那个对象调用自己的呢？

![2016-05-10_213705](C++%20is%20an%20extension%20of%20C.assets/clip_image002-1721634046706.jpg)

c++通过提供特殊的对象指针，this指针，解决上述问题。This指针指向被调用的成员函数所属的对象。

c++规定，this指针是隐含在对象成员函数内的一种指针。当一个对象被创建后，它的每一个成员函数都含有一个系统自动生成的隐含指针this，用以保存这个对象的地址，也就是说虽然我们没有写上this指针，编译器在编译的时候也是会加上的。因此this也称为“指向本对象的指针”，this指针并不是对象的一部分，不会影响sizeof(对象)的结果。

this指针是C++实现封装的一种机制，它将对象和该对象调用的成员函数连接在一起，在外部看来，每一个对象都拥有自己的函数成员。一般情况下，并不写this，而是让系统进行默认设置。

**this指针永远指向当前对象。**

成员函数通过this指针即可知道操作的是哪个对象的数据。this指针是一种隐含的指针，它隐含于每个类的非静态成员函数中。this指针无需定义，直接使用即可。

**注意：静态成员内部没有this指针，静态成员函数不能操作非静态成员变量。**

```c++
class Test{
public:
    Test(int a){
        m_a = a;
    }
    int getA(){
        return m_a;
    }
    static void print(){
        cout << "This is class Test" << endl;
    }
private:
    int m_a;
};

Test a(10);

a.getA();

Test::print();
/*--------------------------------------------------------------------------------------------------------*/
struct Test{
    int m_a;
};

void Test_initialize(Test *pThis, int i){
    pThis->m_a = i;
}

int Test_getA(Test* pThis){
    return pThis->m_a;
}

void Test_print(){
    cout << "hello world" << endl;
}

Test a;
Test_initialize(&a, 10);
Test_getA(&a);
Test_print();
```

#### b.this指针的使用

- 当形参和成员变量同名时，可用this指针来区分
- 在类的非静态成员函数中返回对象本身，可使用return *this

```c++
class Person{
public:
	//1. 当形参名和成员变量名一样时，this指针可用来区分
	Person(string name,int age){
		//name = name;
		//age = age; //输出错误
		this->name = name;
		this->age = age;
	}
	//2. 返回对象本身的引用
	//重载赋值操作符
	//其实也是两个参数，其中隐藏了一个this指针
	Person PersonPlusPerson(Person& person){
		string newname = this->name + person.name;
		int newage = this->age + person.age;
		Person newperson(newname, newage);
		return newperson;
	}
	void ShowPerson(){
		cout << "Name:" << name << " Age:" << age << endl;
	}
public:
	string name;
	int age;
};

//3. 成员函数和全局函数(Perosn对象相加)
Person PersonPlusPerson(Person& p1,Person& p2){
	string newname = p1.name + p2.name;
	int newage = p1.age + p2.age;
	Person newperson(newname,newage);
	return newperson;
}

int main(){

	Person person("John",100);
	person.ShowPerson();

	cout << "---------" << endl;
	Person person1("John",20);
	Person person2("001", 10);
	//1.全局函数实现两个对象相加
	Person person3 = PersonPlusPerson(person1, person2);
	person1.ShowPerson();
	person2.ShowPerson();
	person3.ShowPerson();
	//2. 成员函数实现两个对象相加
	Person person4 = person1.PersonPlusPerson(person2);
	person4.ShowPerson();

	system("pause");
	return EXIT_SUCCESS;
}
```

#### c.const修饰成员函数

- 用const修饰的成员函数时，const修饰this指针指向的内存区域，成员函数体内不可以修改本类中的任何普通成员变量。
- 当成员变量类型符前用mutable修饰时例外。

```c++
//const修饰成员函数
class Person{
public:
	Person(){
		this->mAge = 0;
		this->mID = 0;
	}
	//在函数括号后面加上const,修饰成员变量不可修改,除了mutable变量
	void sonmeOperate() const{
		//this->mAge = 200; //mAge不可修改
		this->mID = 10; //const Person* const tihs;
	}
	void ShowPerson(){
		cout << "ID:" << mID << " mAge:" << mAge << endl;
	}
private:
	int mAge;
	mutable int mID;
};

int main(){

	Person person;
	person.sonmeOperate();
	person.ShowPerson();
	system("pause");
	return EXIT_SUCCESS;
}
```

#### d.const修饰类对象

- 常对象只能调用const的成员函数。
- 常对象可访问const或非const数据成员，不能修改，除非成员用mutable修饰。

```c++
class Person{
public:
	Person(){
		this->mAge = 0;
		this->mID = 0;
	}
	void ChangePerson() const{
		mAge = 100;
		mID = 100;
	}
	void ShowPerson(){
		cout << "ID:" << this->mID << " Age:" << this->mAge << endl;
	}

public:
	int mAge;
	mutable int mID;
};

void test(){	
	const Person person;
	//1. 可访问数据成员
	cout << "Age:" << person.mAge << endl;
	//person.mAge = 300; //不可修改
	person.mID = 1001; //但是可以修改mutable修饰的成员变量
	//2. 只能访问const修饰的函数
	//person.ShowPerson();
	person.ChangePerson();
}
```

## 十二、友元

- friend关键字只出现在声明处
- 其他类、类成员函数、全局函数都可声明为友元
- 友元函数不是类的成员，不带this指针
- 友元函数可访问任意成员属性，包括私有属性

```c++
Class Building;
//友元类
class MyFriend{
public:
    //友元成员函数
    void LookAtBedRoom(Building& building);
    void PlayInBedRoom(Building& building);
};
classBuilding{
    //全局函数做友元函数
    friend void CleanBedRoom(Building& building);
#if 0
    //成员函数做友元函数
    friend void MyFriend::LookAtBedRoom(Building& building);
	friend void MyFriend::PlayInBedRoom(Building& building);
#else
    //友元类
    friend class MyFriend;
#endif
public:
    Building();
public:
    string mSittingRoom;
private:
    string mBedroom;
};
void MyFriend::LookAtBedRoom(Building& building){
    cout <<"我的朋友参观"<<building.mBedroom << endl;
}
voidMyFriend::PlayInBedRoom(Building& building){
    cout << "我的朋友玩耍在" << building.mBedroom << endl;
}
//友元全局函数
void CleanBedRoom(Building& building){
    cout << "友元全局函数访问" << building.mBedroom << endl;
}
Building::Building(){
    this->mSittingRoom ="客厅";
    this->mBedroom ="卧室";
}
int main(){
    Building building;
    MyFriend myfriend;
    CleanBedRoom(building);
    myfriend.LookAtBedRoom(building);
    myfriend.PlayInBedRoom(building);
    system("pause");
    return EXIT_SUCCESS;
}
```

友元类注意:

1. 友元关系不能被继承
2. 友元关系是单向的，类A是类B的朋友，但类B不一定是类A的朋友
3. 友元关系不一定具有传递性。类B是类A的朋友，类C是类B的朋友，但类C不一定是类A的朋友