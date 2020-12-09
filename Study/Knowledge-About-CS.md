# 综合

## 软件源代码集合

```
http://www.columbia.edu/kermit/archive.html
```

## 学习资料

```
http://ebadillo_computacion.tripod.com/
https://www.examcoo.com/index/ku
https://github.com/greyireland/algorithm-pattern/
https://oi-wiki.org/
https://github.com/neolee/wop-ecnu-pub/blob/master/HOWTO.md
https://github.com/cucygh/fe-material
https://github.com/JacksonTian/fks
https://github.com/oldratlee/translations
https://github.com/ahangchen/How-to-Be-A-Programmer-CN
https://github.com/euphrat1ca/security_w1k1
https://github.com/prakhar1989/awesome-courses
https://github.com/No-Github/1earn
https://www.mooc.cn/
https://www.icourse163.org/
https://www.imooc.com/
https://www.waitsun.com/
https://refactoringguru.cn/
https://www.felix021.com/blog/
http://www.dxzy163.com/
https://hoochanlon.github.io/fq-book/#/abc/3vm
https://www.cnblogs.com/hustskyking/p/hosts-modify.html
https://www.cnblogs.com/kunlunmountain/p/5945756.html
```

## 桌面运维管理指南

```
https://hoochanlon.github.io/helpdesk-guide/
https://github.com/hoochanlon/helpdesk-guide
```

## 各校课程共享计划

### 北京大学

```
https://github.com/martinwu42/project-hover/tree/legacy/project-hover
https://github.com/tongtzeho/PKUCourse
https://github.com/lib-pku/libpku
https://lib-pku.github.io/
```

### 清华大学

```
https://github.com/PKUanonym/REKCARC-TSC-UHT
https://rekcarc-tsc-uht.readthedocs.io/en/latest/
```

### 浙江大学

```
https://github.com/QSCTech/zju-icicles
https://qsctech.github.io/zju-icicles/
https://github.com/LeadroyaL/ZJU_ISEE_Project
```

### 东南大学

```
https://github.com/zjdx1998/seucourseshare
```

### 中国科学技术大学

```
https://github.com/ustcwpz/USTC-CS-Courses-Resource
https://github.com/USTC-Resource/USTC-Course
https://ustc-resource.github.io/USTC-Course/
```

### 上海交通大学

```
https://github.com/c-hj/SJTU-Courses
```

### 中山大学

```
https://github.com/sysuexam/SYSU-Exam
```

### 南京大学

```
https://github.com/idealclover/NJU-Review-Materials
```

### 郑州大学

```
https://github.com/CooperNiu/ZZU-Courses-Resource
```

### 华南师范大学

```
https://www.yuque.com/0xffff.one/cs-learning
```

### 广东工业大学

```
https://github.com/brenner8023/gdut-course
https://brenner8023.github.io/gdut-course/
```

### 北京林业大学

```
https://github.com/bljx/BFU-leaf
```

### 山东科技大学

```
https://github.com/deepwzh/sdust-examination-materials
```

# 数据结构

```
https://blog.csdn.net/HGGshiwo/article/details/110287629
```

# Swift

## 资料

```
https://objccn.io/
https://swiftgg.gitbook.io/swift/
https://swift.org/
```

## 设计规范

```
https://developer.apple.com/documentation/
https://developer.apple.com/design/human-interface-guidelines/
https://www.microsoft.com/design/fluent/
https://principleformac.com/tutorial.html
https://material.io/design
```

# C/C++

## 习题

以下程序的运行结果是1342。先构造基类再构造派生类，析构则相反。

```
#include<iostream.h>
class A
{
public:
  A(){cout<<"1";}
  ~A(){cout<<"2";}
};
class B:public A
{
public:
  B(){cout<<"3";}
  ~B(){cout<<"4";}
};
void main()
{
  B b;
}
```

以下程序的运行结果是21。在执行b(a)时，对于num＝a.num++，b.num被赋予1，而a.num自增后变为2。

```
#include<iostream.h>
class A
{
	int num;
public:
	A(int i)
	{num＝i;}
	A(A& a)
	{num＝a.num++;}
	void print( )
	{cout<<num;}
};
void main()
{
	A a(1),b(a) ;
	a.print( ) ;
	b.print( ) ;
}
```

若新建对象时为数组形式，则未被赋值的项默认无参数（而不是0）。

```
#include <iostream>
using namespace std;
class CSample {
       int x;
 public: 
     CSample() { cout<<"C1"<<endl;}
     CSample(int n ) {x = n;cout<<"C2,x="<<x<<endl;}
};
int main()
{
CSample array1[2];
CSample array2[2] = {6,8};
CSample array3[2] = {12};
return 0;
}

// 结果为
C1
C1
C2,x=6
C2,x=8
C2,x=12
C1
```

拷贝构造函数与拷贝复制函数的不同。注意，若`Sample PrintAndDouble(Sample o)`改为`Sample PrintAndDouble(Sample& o)`，则参数拷贝到形参这一步并不执行，答案从80变为40。

```
#include <iostream>
using namespace std;
class Sample{
public:
    int v;
    Sample() { };
    Sample(int n):v(n) { };
    Sample(const Sample & x) { v = 2 * x.v; }
};
Sample PrintAndDouble(Sample o) { // 参数拷贝到形参，调用拷贝构造函数，v*2
	cout << o.v<<endl;
	o.v = 2*o.v; // v*2
	return o; // 形参拷贝到返回值，调用拷贝构造函数，v*2
}
int main()
{ 
	Sample a(5);
	cout << a.v<<endl;
	Sample b = a; // 调用拷贝构造函数，v*2
	cout << b.v<<endl;
	Sample c = PrintAndDouble( b ); // 返回时为v*2*2*2
	cout << c.v<<endl;
	Sample d;
	d = a; // 调用拷贝复制函数，与上面不同
	cout << d.v<<endl;;
	return 0;
}

// 结果为
5
10
20
80
5
```

以下语句生成两个类A的对象，其中第二和第四个为NULL，即未初始化。

```
A *arry[4] ={new A(),NULL,new A()};
```

假设A是一个类的名字，下面程序片段，类A会调用析构函数三次。

```
int main(){
	A *p = new A[2];
	A *p2 = new A;
	A a;
	delete[] p;
}
```

应当在square前的问号加static关键字，因为该函数没有使用类中的数据。引用static函数时注意需要用`::`而不是`.`。

```
#include "iostream"
using namespace std;
class Test {
public:
	? int square(int n)
	{
  		return n * n;
	}
	? int getAge()
	{
  		return age;
	}
private:
	int age;
};

int main() {
	cout<<Test::square(4);
}
```

构造函数与析构函数的执行顺序。函数调用完毕后系统自动调用析构函数，但如果是static类型，则等到程序结束后才调用析构函数。最后析构时先析构main()自身的对象（因main()执行完毕），然后析构其它函数里的静态变量，最后析构全局变量。

```
#include<iostream>
#include<string.h>
using namespace std;
class A
{
public:
 A(char *ps){ strcpy(s,ps); cout<<"1:"<<s<<endl;  }
 ~A(){cout<<"2:"<<s<<endl; }
private:
 char s[50];
};
void Fun()
{    
A FunObject("F");
static A staticObject("S");
cout<<"In Fun()."<<endl;
}
A GlobalObject("G"); 

int main()
{    
A MainObject("M"); 
cout<<"In main(), before called Fun"<<endl;
Fun();
cout<<"In main(), after called Fun"<<endl;
return 0;
}

// 结果为
1:G // 在main()之前有一个GlobalObject
1:M // main()中的MainObject
In main(), before called Fun
1:F // Fun()中的FunObject
1:S // Fun()中的staticObject
In Fun().
2:F // Fun()执行完毕，FunObject被析构，但staticObject为静态，仍保留
In main(), after called Fun
2:M // main()执行完毕，首先析构main()自己的MainObject
2:S // 析构Fun()中静态的staticObject
2:G // 析构全局的GlobalObject
```

根据以下定义，`a[2][1]`的值是5。`a[ ][3]`表示不限制行数，而列数是3。

```
int a[ ][3] = { {1,4}, {3,2}, {4,5,6}, {0} };
```

如果要求在if后一对括号中的表达式在表示a不等于0的时候的值为真，则能正确表示这一关系的表达式为`a<>0`，而非`!a`。

已知int a，*pa=&a，输出指针pa十进制的地址值的方法是`cout<<long(&pa);`。

给定以下类声明，`f1和f2`可能改变成员变量data。

```
class A {
public:
	void f1 (int d);
	void f2 (const int &d);
	void f3 (int d) const;
private:
	int data;
};
```

虚函数和继承。

```
#include <iostream>
using namespace std;
class A {
public:
  A() { }
  virtual void func(){ cout <<"A::func" <<endl; }
  ~A() { }
  virtual void fund(){ cout <<"A::fund" << endl;}
};
class B:public A {
public:
  B() { func(); }
  void fun() { func(); }
  ~B() { fund(); }
};
class C : public B {
public :
  C() { }
  void func(){ cout <<"C::func"<<endl;}
  ~C() { fund();}
  void fund(){ cout <<"C::fund"<<endl;}
};
int main()
{ C c;
  return 0;
} 

// 结果为
A::func // 构造C需先构造B，构造B需先构造A。A构造不做动作，B构造调用的func()为A中的（因为此时C并没有构造出来），输出此句。C构造不做动作
C::fund // 析构时先析构C，调用的fund()为C中的（先从自己开始），输出此句
A::fund // 继续析构B，调用的fund()为A中的（C已被销毁），输出此句。析构A不做动作，结束
```

操作符重载。

```
#include<iostream>
using namespace std;
class A{   
public:
    A& operator=(const A& r)
    {
        cout << 1 << endl;
        return *this;
    }
};
class B{   
public:
    B& operator=(const B& r)
    {
        cout << 2 << endl;
        return *this;
    }
};
class C{
private:
    B b;
    A a;
    int c;
};

int main()
{
    C m,n;
    m = n;
    return 0;
}

// 结果为
2 // 执行m=n时，按照定义顺序，首先执行B的赋值，然后执行A的赋值
1
```

```
设有定义`vector<string> v(10);`，执行下列（C）时会调用构造函数。
A. v[0] += “abc”;
B. v[0] = “2018”;
C. v.push_back(“ZUCC”);
D. cout << (v[1] == “def”);

下列对模板的声明中正确的是（C）。
A．template＜T＞
B．template＜class T1，T2＞
C．template＜class T1,class T2＞
D．template＜class T1；class T2＞
```

设有如下代码段，读入10个字符串，则输出的m.size()为1，因为没有使用map的insert()方法。

```
std::map<char *, int> m;
const int MAX_SIZE = 100;
int main() {
	char str[MAX_SIZE];
	for (int i = 0; i < 10; i++) {
   		 std::cin >> str;
    	 m[str] = i;
	}
   std::cout << m.size() << std::endl;
}
```

```
下列创建vector容器对象的方法中，错误的是（D）。

A.vector<int> v(10);
B.vector<int> v(10, 1);
C.vector<int> v{10, 1};
D.vector<int> v = (10, 1);
```

以下程序的输出结果是66。进入了`void fun(char ch)`函数，但`cout`按照数字的格式进行了输出。

```
#include <iostream>
using namespace std;
void fun(int num)
{
    cout << num << endl;
}
void fun(char ch)
{
    cout << (ch + 1) << endl;
}
int main()
{
    fun('A');
    return 0;
}
```


```
#include<iostream> 
using namespace std;

class A{
public:
  A(){}
  virtual int output() = 0;
private:
  int i;
};

class B : public A{
private:
  int j;
};

class C{
public:
  int f(int a){ return x*a; }
protected:
  void setX(int a){ x = a; }
  int getX(){ return x; }
private:
  int x;
};

class D : public C{
private:
  int z;
};

int main(){
  A* pA = NULL;
  A objA;     
  B objB;
  C objC;
  D objD;

  objC.setX(2);
  cout << objC.getX();

  objC.setX(1);
  objC.f(3);

  return 0;
}

// 不能通过编译的有
A objA;
B objB; // 均为虚基类
objC.setX(2);
cout << objC.getX();
objC.setX(1); // 均为protected类型
```

```
  class A {
  virtual void f() { cout << "lala"; }
  public:
    void f(int a, int b=10) {cout << a+b; }
    void f(int a, int b=10) const {cout << a-b; }
  };

  class B : public A {
  public:
    void f() { cout << "lili"; }
  };

  int main()
  {
    B a;
    A * p = &a;
    p->f();
    return 0;
  }

// 错误在于A中的f()不能是private的
```

```
class Obj {
    char c; 
public:
    Obj(char cc){
         c = cc;
        cout << "Obj::Obj(char cc) for " << c << endl;
    }
    ~Obj() {
       cout << "Obj::~Obj() for " << c << endl;
    }
};
void f() {  static Obj b('b'); }
void g() {  Obj c('c'); }

Obj a('a'); 
int main() 
{
 cout << "inside main()" << endl;
     f();
     g();
     f();
     g();
     cout << "leaving main()" << endl; 
     return 0;
}

// 结果为
Obj::Obj(char cc) for a // 全局变量
inside main()
Obj::Obj(char cc) for b // 静态变量，待程序结束后被销毁
Obj::Obj(char cc) for c
Obj::~Obj() for c // 局部变量，函数结束即销毁
Obj::Obj(char cc) for c // 静态变量b已被定义，不再重新分配空间
Obj::~Obj() for c // 局部变量，函数结束即销毁
leaving main()
Obj::~Obj() for b // 先销毁静态变量
Obj::~Obj() for a // 最后销毁全局变量
```

有无引用的区别。使用引用作为函数参数时，所做的修改会直接反映到该变量上，与普通的形参不同，

```
int f(int a)
{
    return ++a;
}
int g(int& a)
{
    return ++a;
}

void main()
{
    int m = 0, n = 0;
    m += f(g(m));
    n += f(f(n));
    cout << "m=" << m << endl;
    cout << "n=" << n << endl;
}

// 答案为
3 // 调用g(0)，返回1，此时m已经是1（即在引用中，对a的操作会直接反映到该变量上），再调用f(1)，返回2，则1+2=3
2 // 调用f(0)，返回1，但此时n仍为0（对形参的改变不影响其本身），所以是1+1=2
```

改错题。

```
#include <typeinfo.h>
#include <iostream.h>
class A
{
  int m_x;
public:
};
class B : public A
{
  int m_y;
public:
  B(int x = 0,int y = 0){ m_x = x; m_y = y; }
};
void main()
{
  A *ap=new B;
  cout<<typeid(*ap).name()<<endl;
}

// 改正为
#include <typeinfo.h>
#include <iostream.h>   
class A
{
protected:
  int m_x;        //m_x变量声明为protected
public:
  virtual ~A(){}          //增加虚析构函数
};
class B : public A
{
  int m_y;
public:
  B(int x = 0,int y = 0){ m_x = x; m_y = y; }
};
void main()
{
  A *ap=new B;
  std::cout<<typeid(*ap).name()<<std::endl; 
   delete ap;      //增加delete ap
}

```

```
class A
{
public:
    static int f1() const
    {
        return m_i;
    }
    static int f2() const
    {
        return m_s;
    }
    static int f3() const
    {
        return ++m_i;
    }
private:
    int m_i;
    static int m_s;
};
int A::m_s = 0

// 改正为
class A
{
public:
    int f1() const      //去掉static（m_i不是静态变量）
    {
        return m_i;
    }
    static int f2()     //去掉const（处理静态变量不用const）
    {
        return m_s;
    }
    int f3()     //去掉static和const（m_i不是静态变量，且此处对m_i进行了修改）
    {
        return ++m_i;
    }
private:
    int m_i;
    static int m_s;
};
int A::m_s = 0;

```

## 基础知识

### 面向对象系统

结构化程序设计具有很多优点，是一种面向过程的程序设计方法。

在面向对象分析和设计中，通常把对象所进行的操作称为方法。

面向对象程序设计的主要特征有封装、继承、多态。

在面向对象系统中，对象是基本的运行时实体。它既包括数据（属性），也包括作用于数据的操作（行为），一个对象把属性和行为封装为一个整体。

在面向对象系统中，对象的属性是和其他对象相互区分的特性。属性可以是基本变量，也可以是一个对象。

在面向对象的软件系统中，不同类对象之间的通信的一种构造称为消息。其组成部分包括接受消息的对象、要执行的函数的名字、函数需要的参数。

在C++中用类将数据和对数据操作的代码连接在一起称为封装。封装使对象的内部实现与外界隔离，实现了信息隐藏，从而提供了更理想的模块化机制，显著地减少了程序模块间的相互干扰和依赖性。

源程序文档化一般应注意符号名的命名、程序的注释和视觉组织。

### 输入输出

C++语言本身没有输入输出语句。

### 函数

不写return 0时main函数一般返回-1。

函数的定义不能嵌套，但函数调用可以嵌套。

被调用函数中可以不用return语句，也可以使用多个return语句。

### 指针

类型相同的两个指针变量之间不能进行`+`运算，可进行`<`、`=`、`-`运算。`<`（小于）运算在两个同类型的指针间可以比较大小，比较原则应该是按照实际内存的高低位比较的。`=`（等于）是对于类型相同的两个指针变量之间常规运算。`-`（减法）运算两个相同指针变量相减可以获得在之间相隔的同类型元素个数（在某个类型的数组中的应用）。

### 注释

注释说明了程序的功能，它包括序言性注释和功能性注释。

注释符中`/*`和`*/`不能嵌套使用，但可与`//`嵌套使用。

### 结构体

不能将结构体变量作为一个整体进行输出或输入。

### 编译预处理

编译预处理指令不是C++语言中不可或缺的重要部分，因为编译预处理命令不是C语言文本的内容，是编译工具的功能，不同的编译工具，其功能及命令是可能不同的。

### 交换值的方法

```
x=x+y, y=x-y, x=x-y;
t=x, x=y; y=t;
t=y, y=x, x=t;
```

## 类与对象

### 基本知识

类通过接口与外界发生关系。为类提供对外接口的是公有成员函数。

类是对象的抽象，对象是类的实例，系统为对象分配存储空间而不为类分配存储空间。

### 构造函数

标准C语言的struct结构没有构造函数。

构造函数通过对象只能调用一次。构造函数不能有返回值，但可以被重载。

类不能没有构造函数，但可以缺省。

带参数的构造函数并不具有类型转换作用。

必须采用构造函数的初始化列表方式进行初始化的情况有：内嵌对象、常数据成员、对虚基类的初始化。静态数据成员不能采用构造函数的初始化列表方式进行初始化。

派生类构造函数，需要为基类构造函数传递参数、为内嵌子对象的初始化传递参数，可以采用初始化列表的方式为常成员函数传递初始化参数。

对象数组在定义过程中进行元素的初始化时，调用有参构造函数。如果只定义对象数组而不进行初始化，此时调用的是无参构造函数。

以下写法是正确的。

```
class Base{
private:
  int a;
public:
  Base(int a=0):a(a){};
}
```

### 拷贝构造函数

当用一个对象去初始化同类的另一个对象时，要调用拷贝构造函数。但对象间赋值并不调用拷贝构造函数，而是调用拷贝复制函数。例子如下，假设有一个类为cla。

```
// 拷贝构造函数
cla a(5);
cla b(a); // 或cla b=a;

// 拷贝复制函数（即=的重载函数）
cla a(5);
cla b(0);
a=b;
```

当类的数据成员中有指针类型时，必须定义一个特定的拷贝构造函数，该拷贝构造函数不仅可以实现原对象和新对象之间数据成员的复制，而且可以为新的对象分配单独的内存资源，这就是深拷贝构造函数。

设A为自定义类，现有普通函数`int fun(A& x)`。则在该函数被调用时无需初始化形参x。但`int fun(A x)`则需要调用拷贝构造函数。

如果函数的返回值是类A的对象，则函数返回时，类A的拷贝构造函数将被调用。

### 析构函数

析构函数的作用主要是释放对象占用的资源，在删除对象时被调用。系统可以自动调用析构函数。

析构函数不是系统的内置函数。

构造函数可以重载，析构函数不能重载。析构函数不能返回任何值。

一个类只能定义一个析构函数，但可以定义多个构造函数。

### 成员函数

成员函数可以重载。

一个类可以没有成员函数。

成员函数可以设置参数的默认值。

### this指针

类的非静态成员函数才有this指针。const成员函数内部、构造函数内部、析构函数内部可以使用this指针。

this指针是系统内预定义的特殊指针。this的值不能改变，它总是指向当前调用对象，表示当前对象的地址。当创建一个对象后，this指针就指向该对象。

this指针不是调用对象的名称，而是指向调用对象的指针名称。

当一个对象调用其成员函数时，编译器先将该对象的地址赋给this指针，然后调用成员函数，这样成员函数对对象的数据成员进行操作时，就隐含使用了this指针。

### 对象成员

对于类类型对象成员的引用方式，在语法上与结构体类型变量成员的引用方式是一样的。

指向类的数据成员指针所指向的数据成员，可以具有公有属性，但不能具有私有属性。

类的成员函数之间可以相互调用。

### 静态成员

静态成员的特点是不管这个类创建了多少个对象，其静态成员在内存中只保留一份副本，这个副本为该类的所有对象共享，或者说静态成员为类所有。

静态成员不能在类中初始化，使用时需要在类体外声明。

静态数据成员不属于某个对象，没有this指针。在给对象分配存储空间时，不包括静态数据成员所占的空间。

静态数据成员可以通过对象名引用，也可以通过类名引用。

静态成员函数能直接访问的是静态数据成员、静态成员函数、类以外的函数和数据。

静态成员函数只能引用类中声明的静态数据成员。如果要引用非静态数据成员，必须通过对象引用。

静态成员的定义举例如下。

```
#include "iostream"
using namespace std;
class Test{
public:
  static int square(int n)
  {
  	return n * n;
	}
	int getAge()
	{
	  return age;
	}
	static int k;
private:
	int age;
};

int Test::k = 5;
```

### 常成员

常成员是指使用常类型修饰const修饰说明的成员，分为常成员函数和常数据成员。

常数据成员必须被初始化，并且不能更新。

在类中声明常数据成员时，只能通过构造函数成员初始化列表的方式来实现。

`const char *p;`说明了p是可以指向字符常量的指针。

常成员函数可以引用对象的数据成员，但是不能更新数据成员，也不能调用该类中非常成员函数。常量对象可以使用常量成员函数。

常成员函数既可以被常对象调用，也可以被非常对象调用。

两个函数，名字和参数表都一样，但是一个带const，一个不带，也算重载。

在常成员函数中，const是函数类型的一部分，在函数声明和函数定义部分都要有该关键字。

只有常成员函数可以操作常对象。

已知print()函数是一个类的常成员函数，它无返回值，则定义如下。

```
void print() const;
```

### 友元

函数模板的友元函数必须是模板函数。

友元函数不是当前类的成员函数，而是独立于当前类的外部函数，但可以访问类的私有成员变量或者是成员函数。

如果A是B的友元类，那么A的成员函数可以访问B的私有成员。

可以将一个类的成员函数（包括构造和析构函数）说明为另一个类的友元。

友元类之间的关系不能传递，但能继承。即已知类A是类B的友元，类B是类C的友元，则类A并不一定是类C的友元。

友元类举例如下。

```
class A{
	...
}

class B{
	friend class A; // A为B的友元类，A可访问B中的成员
}
```

### 访问属性

类成员的默认访问属性是private。

### 内联函数

一般情况下，将函数的代码量小、调用次数多的函数说明为内联函数。

### 函数重载

返回类型不能作为函数重载判断依据。

### new运算符

使用new创建对象时必须定义初始值。

通过new运算符生成的对象，执行delete操作时才能析构。若new一个对象数组，那么释放时应该写`delete[]`，否则只delete一个对象（调用一次析构函数）。

## 继承与派生

### 基本知识

|          | public    | protected | private |
| -------- | --------- | --------- | ------- |
| 公有继承 | public    | protected | 不可见  |
| 私有继承 | private   | private   | 不可见  |
| 保护继承 | protected | protected | 不可见  |

继承是指使用已经存在的类定义作为基础建立新的类定义的技术。继承机制的作用是定义新类。

一个类只允许有一个父类，这样的继承称为单继承。

派生类是基类的组合。

类的组合关系可以用`Has-A`描述；类间的继承与派生关系可以用`Is-A`描述。

公有继承时，水平访问和垂直访问对基类中的公有成员不受限制；私有继承时，水平访问和垂直访问对基类中的公有成员也不能访问；保护继承时，对于垂直访问（类的内部）等同于公有继承，对于水平访问（两个类之间）等同于私有继承。

基类中的私有成员不论通过何种派生方式，到了派生类中均变成不可直接访问成员。

对于基类中的私有成员，只能被基类中的成员函数和友元函数所访问，不能被其他的函数访问。

如果公有派生，则可以用派生类对象给基类对象赋值，但不能用基类对象初始化派生类的引用。

派生类对象可以分配给基类对象，基类对象可以被分配给派生类对象。

派生类对象指针可以分配给基类对象指针，但基类对象指针不能分配给派生类对象指针。

派生类的构造函数会隐含调用基类的构造函数。如果基类声明了带有形参表的构造函数，则派生类就应当声明构造函数。

派生类不能继承构造函数、析构函数。

如果基类有无参构造函数，则派生类的构造函数可以不带初始化列表。

在建立派生类对象时，先调用基类的构造函数，再调用成员对象的构造函数，最后调用派生类的构造函数。销毁则相反。举例如下。

```
#include<iostream>
using namespace std;
class A
{
public:
    A(){cout<<"A";}
};
class B
{
public:
    B(){cout<<"B";}
};
class C:public A
{
    B b;
public:
    C(){cout<<"C";}
};
int main(){
    C obj;
    return 0;
}

// 结果为ABC
```

关于成员对象的构造函数，其在该类的构造函数之前、该类的基类的构造函数之后被调用。举例如下，

```
class MyClass
{
public:
  MyClass(int id) { cout << "MyClass::Ctor\n"; }
  ~MyClass() { cout << "MyClass::Dtor\n"; }

private:
  int id;
};

class Base
{
public:
  Base(int _id):myclass(_id) { cout << "Base::Ctor\n"; }
  ~Base() { cout << "Base::Dtor\n"; }
  virtual void foo() { cout << "Base::foo\n"; }
private:
  MyClass myclass;
};

class Derived : public Base
{
public:
  Derived(int _id):Base(_id){ cout << "Derived::Ctor\n"; foo(); }
  ~Derived(){ cout << "Derived::Dtor\n"; foo(); }
  virtual void foo() { cout << "Derived::foo\n"; }
private:

};

int main()
{
  Base* p = new Derived(10);
  delete p;
  return 0;
}

// 结果为
MyClass::Ctor
Base::Ctor
Derived::Ctor
Derived::foo
Base::Dtor
MyClass::Dtor
```

若为多继承，则按照书写顺序进行构建，举例如下。

```
#include<iostream>
using namespace std;
class A 
{
private:
    int a;    
public:
    A(int i) {a=i;cout<<"A constructor"<<endl;}
};
class B
{
private:
    int b;
public:
    B(int j) {b=j;cout<<"B constructor"<<endl;}
};
class C: public B,public A
{
private:
    int c;

public:
    C(int k):A(k-2),B(k+2) {c=k;cout<<"C constructor"<<endl;}
};

int main(){
	C c(10);
}

// 结果为
B constructor
A constructor
C constructor
```

### 子对象

某个类B有数据成员a，且a是另一个类A的对象，则a是B的子对象。

当一个类中出现了子对象，该类的构造函数负责子对象的初始化。子对象的初始化应通过构造函数的初始化列表来完成。

## 多态性和虚函数

多态从实现的角度分为静态多态和动态多态。

动态绑定是以虚函数为基础的。

动态绑定在运行时确定所调用的函数代码。

动态绑定调用函数操作是通过指向对象的指针或对象引用来实现的。

动态绑定是在运行时选定调用的成员函数的。

动态联编实现的条件：要有说明的虚函数、调用虚函数操作的是指向对象的指针、调用虚函数操作的是对象引用。

类的静态成员函数不可以定义为虚函数。

在构造函数中调用虚函数，不是动态联编。

构造函数不能为虚函数，析构函数可以是虚函数。虚析构函数的作用是delete动态对象时释放资源。

虚函数必须是类的成员函数。

在派生类中，重载一个虚函数时，要求函数名、参数的个数、参数的类型、参数的顺序和函数的返回值相同。

在C++语言中设置虚基类的目的是解决多继承造成的二义性问题。

派生类的虚函数与基类的虚函数具有相同的参数个数和类型。

在构造函数中调用虚函数不是动态联编。构造函数和析构函数调用虚函数时都不使用动态联编，如果在构造函数或析构函数中调用虚函数，则运行的是为构造函数或析构函数自身类型定义的版本。

抽象类是指具有纯虚函数的类。抽象类只能作为基类来使用，其纯虚函数的实现由派生类给出。抽象类不能用做参数类型、函数返回类型或显式转换的类型。可以定义指向抽象类的指针和引用，但不能定义抽象类对象。示例如下。

```
// 假设Aclass为抽象类
Aclass *p;
int fun(Aclass& );
```

另一示例如下。

```
class B : public A{
public:
void f() {}
void g() {}
private:
};
void fun(B b){};
// 此处错误，B是抽象类，不能新建B的对象，故应为void fun(B& b){};
```

不能说明其对象是抽象类的特性。

抽象类指针可以指向不同的派生类。

基类中采用virtual说明一个虚函数后，派生类中定义相同原型的函数时可不必加virtual说明。

纯虚函数是一种特殊的虚函数，它没有具体的实现。如下可将show声明为不带返回值的纯虚函数。

```
virtual void show()=0;
```

虚函数示例如下。

```
class A {
public:
    void func1() {
        cout << "A1" << endl;
    }
    virtual void func2() {
        cout << "A2" << endl;
    }
};
class B : public A {
public:
    void func1() {
        cout << "B1" << endl;
    }
    void func2() {
        cout << "B2" << endl;
    }
};

int main() {
    A *a = new B;
    a->func1();
    a->func2();
}

// 结果为A1 B2
// a->func1()时，由于a属于类A的对象，因此直接调用类A的func1()。a->func2()时，由于类A的func2()为虚函数，而类B中有非虚函数的func2()，因此调用类B的func2()
```

另一示例如下。

```
#include <iostream>
using namespace std;
class A {
public:
A( ) { }
virtual void func()
{ cout << "A::func" << endl; }
virtual void fund( )
{ cout << "A::fund" << endl; }
void fun()
{ cout << "A::fun" << endl;}
};
class B:public A {
public:
B ( ) { func( ) ; }
void fun( ) { func( ) ; }
};
class C : public B {
    public :
C( ) { }
void func( )
{cout << "C::func" << endl; }
void fund()
{ cout << "C::fund" << endl;}
};
int main()
{
A * pa = new B();
pa->fun();
B * pb = new C();
pb->fun();
return 0;

// 结果为
A::func // 新建pa时，类B需先新建类A，类A新建完成后新建类B时调用func()，由于类B没有func()且qa为类A，因此执行类A的func()
A::fun // 调用pa->fun()时，由于pa是类A，因此执行类A的fun()
A::func // 新建pb时，类C需先新建类B，类B需先新建类A，因此同第一个
C::func // 调用pb->fun()时，由于pa是类B，因此执行func()，类B没有func()，但pb是类C转过来的，因此执行类C的func()
}
```

关于将派生类赋给基类指针后函数的调用问题。假设B类为派生类，A类为基类，A类和B类均有函数func()。现有两个对象a（A类）和b（B类），且`A& p=b;`。

若func()不为虚函数，则执行`p.func()`时直接调用A类；若为虚函数，则从B类开始寻找func()，若B类有该函数则执行B类的。

```
  class A {
    int i;
  public:
    virtual void set(int ii) { i = ii;}
    virtual int get() { return i; }
  };

  class B : public A {
    int i;
  public:
    virtual void set(int ii) { i = ii;}
    virtual int get() { return i; }
  };

  int main()
  {
    B a;
    B b;
    a.set(10); // B类的i=10
    b.set(20); // B类的i=20
    A& p = a;
    p.set(30); // B类的i=30
    p = b;
    p.set(40); // B类的i=40
    cout << a.get(); // B类的get()
    return 0;
  }

// 结果为40
```

```
class A{
public:
  int f(){ return 1; }
  virtual int g(){ return 2; }
};

class B : public A{
public:
  int f(){ return 3; }
  virtual int g(){ return 4; }
};

class C : public A{
public:
  virtual int g(){ return 5; }
};

int main(){
A *pa;
  A a;
  B b;
  C c;

  pa = &a; 
  cout << pa->f() << endl; 
  cout << pa->g() << endl;
  pa = &b; 
  cout << pa->f() + pa->g() << endl;
  pa = &c; 
  cout << pa->f() << endl; 
  cout << pa->g() << endl;

  return 0;
}

// 结果为
1 // A类的f()
2 // A类的g()
5 // A类的f()，B类的g()
1 // A类的f()
5 // C类的g()
```


```
  class A {
    int i;
  public:
    A() : i(0) {}
    virtual void set(int i) { this->i = i; }
    virtual int get() { return i; }
  };

  class B : public A {
    int i;
  public:
    B() : i(10) {}
    virtual void set(int i) { this->i = i; }
  };

  int main()
  {
    B b;
    A* p = &b;
    p->set(30);
    cout << p->get();

    return 0;
  }

// 结果为0（调用B类的set，A类的get）
```

虚析构函数示例如下。

```
#include <iostream>
using namespace std;
class A {
public :
 virtual ~A() {cout<<"DestructA" <<endl; }
};
class B: public A {
public:
 virtual ~B() {cout<<"DestructB" << endl; }
};
class C: public B {
public:
 ~C() { cout << "DestructC" << endl; }
};
int main() {
A * pa = new C;
delete pa; 
A a;
return 0;
}

// 结果为
DestructC // 删除pa时按照顺序从C开始
DestructB // C被销毁后需销毁B，唯有B的析构函数能够使用
DestructA // 同理销毁A
DestructA // 删除a时调用A的析构函数
```

## 操作符重载

多数运算符可以重载，个别运算符不能重载，运算符重载是通过函数定义实现的。

对单目运算符重载为友元函数时，可以说明一个形参。而重载为成员函数时，不能显式说明形参。

友元函数用于操作符重载时不要使用引用。

```
// 正确
friend const Rational operator*(const Rational& lhs, const Rational& rhs);

// 错误
friend const Rational &operator*(const Rational& lhs, const Rational& rhs);
```

重载运算符可以保持原运算符的优先级和结合性不变。

预定义的提取符和插入符是可以重载的。

每个可重载的运算符并不一定都可以重载为友元函数、成员函数、非成员函数。一些运算符（如下标运算符`[]`，函数调用运算符`()`，赋值运算符`=`等）只能重载为成员函数，而左操作数为std::cout或std::cin的输入输出运算符只能重载为非成员函数。是否是友元函数跟是否是成员函数是两个相互交叉的分类标准。

赋值操作符可以重载，无论形参为何种类型，赋值操作符必须定义为成员函数，而且，赋值必须返回对*this的引用，赋值操作符必须防止自身赋值。而且赋值操作符不应该为虚函数。

对于++运算符的重载，要求重载两种形式，一种是前置++，一种是后置++。前置++不带参数，后置++带参数。示例如下。

```
// 前置++
counter& counter::operator++()    
{
    value += 1;
    return *this;
}

// 后置++
int counter::operator++(int) 
{
    int t = value;
    value += 1;
    return t;
}
```

运算符`()`、`[]`、`->`、`=`只能重载为成员函数。

重载为成员函数时，参数个数为运算符数目减一。重载为友元函数时，参数个数为运算符数目。示例如下。

```
class A {
	int x;
public:
	A(int t=0):x(t){}
	int operator+(const A& a1){ return x+a1.x;  }
};
```

从实现角度看，分为静态多态性和动态多态性。静态多态性包括函数重载和运算符重载，运行时的多态包括继承和虚函数。

多态的实现有函数重载、运算符重载、虚函数。

函数重载和运算符重载都属于重载多态。

按照联编所进行的阶段不同，可分为两种不同的联编方法：静态联编和动态联编。

动态联编对函数的选择不是基于指针或者引用，而是基于对象类型，在编译、链接过程中无法解决的绑定问题要等到程序开始运行之后再确定。

## STL模版

可以通过下标随机访问向量vector中的元素。

当向量对象的内存用完之后，不会产生越界错误。

vector容器和list容器都是序列式容器，其用于排序的sort()函数并不是它们的成员函数。

STL中的关联型容器set只有insert(elem)方法而没有insert(pos, elem)方法。

STL中关联型容器并不一定都是有序的。

映射容器里存储的元素都是成对存在的。

迭代器是对象，它的作用类似于指向元素的指针。STL算法使用迭代器间接地对容器中的元素进行操作。

set/multiset的区别是set用来存储一组无重复的元素，而multiset允许存储有重复的元素。

双向链表list的方法merge()可以将两个list容器合并，在使用merge()方法之前如果两个list容器中的数据是有序的（如从小到大排列），那么两个容器合并后，容器中的元素仍然是有序的（如从小到大排列）。

建立类模板对象的实例化过程为模板类-对象。

在C++中，容器是一种标准类。

类模板从普通类派生，也可以从类模板派生。根据建立对象时的实际数据类型，编译器把类模板实例化为模板类。函数的类模板参数须通过构造函数实例化。

类模板的参数可作为数据成员的类型、成员函数的返回类型、成员函数的参数类型。

模板函数的真正代码是在源程序中调用函数时产生的。

模板说明示例如下。

```
template <typename T1, typename T2>
```

STL的内容从广义上讲分为容器、迭代器、算法三个主要部分。

STL的一个基本理念就是将数据和操作分离。

STL中的所有组件都由模板构成，其元素可以是任意类型。

迭代器有输入迭代器、前向迭代器、双向迭代器等，但没有删除迭代器。

## 异常处理

what()函数定义在exception类中。

C++本身或者标准库抛出的异常都是exception的子类，称为标准异常（Standard Exception）。因此自定义的exception类必须继承自标准异常类，即exception的子类的子类。

throw的使用示例如下。

```
try {
statement1;
statement2;
statement3;
}
catch (Exception1 ex1) { }
catch (Exception2 ex2) { }
catch (Exception3 ex3) { statement4; throw; }
statement5;

// statement2抛出Exception2错误，最后执行statement5
// statement3抛出Exception3错误，最后执行statement4，不执行statement5（已经被抛出，交由其它代码处理）
```

编译错不是异常，运行错、硬件故障属于异常。

C++的异常处理机制具有为抛出异常前构造的所有局部对象自动调用析构函数的能力。异常类对象抛出后，catch块会用类对象引用接收它以便执行相应的处理动作。若catch块采用异常类对象接收异常信息，则在抛出异常时将通过拷贝构造函数进行对象复制，异常处理完后才将两个异常对象进行析构，释放资源。

处理不了的异常，可以通过在catch结构中调用throw重新抛出异常，将当前异常传递到外部的try-catch结构中。重抛异常时只能从catch语句块或从catch块中的调用函数中完成，不能在try语句块中完成。重抛的异常不可以被同一个catch语句捕捉。可以单独使用throw关键字完成异常重抛。

断言是调试程序的一种手段。若断言情况发生，一般会终止程序。在C++中，宏assert()用来在调试阶段实现断言。

C++处理异常的机制由检查、抛出和捕获组成。

错误在try块被抛出后，try后面的代码将不被执行。示例如下。

```
int main() {
double temperature; cin >> temperature; try
{
if (temperature > 95) throw temperature;
cout << "A" << endl; }
catch (double temperature) {
cout << "B " << temperature << endl; }
cout << "C" << endl;
return 0; }

// 结果为
B 120
C
```

## 强制类型转换

`dynamic_cast`是将基类对象的指针或引用转换到继承类指针。

若对指针进行dynamic_cast，失败返回null，成功返回正常cast后的对象指针；若对引用进行dynamic_cast，失败抛出一个异常，成功返回正常cast后的对象引用。

dynamic_cast在将父类cast到子类时，父类必须要有虚函数，否则编译器会报错。

dynamic_cast上行转换（子类变为父类）时，子类指针等于子类对象，即可完成转换；下行转换（父类变成子类）时，需要父类指针指向子类对象，dynamic_cast只是把父类指针这一类型转化为子类指针，并不对子类对象进行处理。因此父类指针指向父类对象是无法完成转换的。

```
class A
{
public:
  virtual ~A(){}
};
class B : public A
{
};
void main()
{
   B *bp;
   B b;
   A a1;
   A &a2 = b;
   try{
      bp = dynamic_cast<B *>(&a1); // 下行转换（&a1实际指向父类对象），含有不安全操作，返回NULL
      if (bp)
      cout << "Dynamic_cast  (1) OK!"<<endl;
      else
      cout << "Dynamic_cast  (1) Fail!"<<endl;
      bp = dynamic_cast<B *>(&a2); // 下行转换（&a2实际指向子类对象），操作安全
      if (bp)
      cout << "Dynamic_cast  (2) OK!"<<endl;
      else
      cout << "Dynamic_cast  (2) Fail!"<<endl;
      B &b1 = dynamic_cast<B &>(a1);// 下行转换（a1实际为父类引用），含有不安全操作，返回NULL
      cout << "Dynamic_cast  (3) OK!" <<endl;
   }
   catch(...){
      cout << "Dynamic_cast (3) Fail!"<<endl;
   }
}

// 结果为
Dynamic_cast  (1) Fail!
Dynamic_cast  (2) OK!
Dynamic_cast (3) Fail!
```


```
void f(double i) { cout << "f(double)" << i << endl; } template <typename T>
void f(T i) { cout << "f(T)" << i << endl; }
int main()
{
f(1);
f(1.0); return 0; }

// 结果为
f(T)1
f(double)1
```

## 流类库与输入输出

| 文件流打开模式 | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| ios::in        | 文件打开为读取（输入）状态，此为istream类的默认模式参数      |
| ios::out       | 文件打开为写入（输出）状态，可实现创建一个可以写入的、新的空文件，如果该文件已经存在，则先删除以前的内容，再写入新数据。此为ostream类的默认模式参数      |
| ios::ate       | 打开一个现存文件，从文件结尾处读取（输入）或写入（输出）     |
| ios::app       | 打开一个输出文件从文件结尾写入（输出）数据                   |
| ios::trunc     | 打开一个文件，如果它已经存在，就删除其中原有的内容           |
| ios::nocreate  | 如果一个文件存在则打开它，否则该操作失败                     |
| ios::noreplace | 如果一个文件不存在则作为新文件打开它，如果文件已存在， 则该操作失败 |
| ios::binary    | 以二进制模式打开一个文件，默认是文本模式                     |

使用`myFile.open("Sales.dat",ios::app);`语句打开文件Sales.date后，则该文件只能用于输出。默认只读模式打开文件，ios:app和ios:app｜ios:out含义一致，只能输出不能输入。

ios类派生istream和ostream，istream派生ifstream，ostream派生ofstream，istream和ostream共同派生iostream，iostream派生fstream。

ifstream类、ostream类和fstream类都提供了成员函数open()用于打开文件。

| 状态标志 | 含义     |
| -------- | -------- |
| dec      | 十进制   |
| oct      | 八进制   |
| hex      | 十六进制 |

以下代码的输出结果为` 2531`。setw(3)设了三个空位，25填了两个。oct转换为八进制，25的八进制为31。hex此处无作用。

```
cout<<setw(3)<<25<<oct<<25<<hex<<endl;
```

记录流的当前格式化状态标志字中的每一位用于记录一种格式，这种格式是可以被设置或清除的。设置和清除格式标志字的成员函数需要通过对象来引用它们，输出显示格式的对象通常是cout。

操纵符本身是一个对象，它可以直接被提取符或插入符操作。

get()函数不能从流中提取终止字符，终止字符仍留在流中。getline()函数可以从流中提取终止字符，但终止字符被丢弃。

使用打开文件函数open()之前，需要定义一个流类对象，使用open()函数来操作该对象。使用关闭文件函数close()关闭一个文件时，流对象仍存在。

以app方式打开文件时，当前的读指针和写指针都定位于文件尾。

read()和write()函数可以读写文本文件，也可以读写二进制文件。

流的状态包含流的内容、长度和下一次提取或插入操作的当前位置。

seekg()函数和seekp()函数分别用来定位读指针和写指针。

当文件输出流写文件的打开模式为trunc时，若文件已存在，则删除文件内容。

ios类是istream类和ostream类的虚基类。iostream不是ios直接派生的子类。

read()函数只能按规定读取所指定的字符数。

当使用ifstream流类定义一个流对象并打开一个磁盘文件时，文件的隐含打开方式为ios::in。

在C++中，打开一个文件，就是将整个文件与一个流建立关联，关闭一个文件，就是取消这种关联。

使用操作符setw对数据进行格式输出时，需要包含iomanip.h文件。

cin和cout是由I/O流库预定义的对象。

填充应写为`cout.fill('#")`而非`cout<<fill('#")`。C++系统默认的填充字符为空格。

IO流的格式化设置中，setf()函数用于设置状态标志，unsetf()函数用于清除状态标志，flags()函数用于获取流状态标志位。它们并不都包含在iomanip头文件中。

IO流状态可以设置和复制。

在C++中，系统提供了一个缓冲区流类库，它是以streambuf为父类的类层次，主要完成信息通过缓冲区的交换。C++中，系统提供的缓冲区是内存的一部分。系统提供的缓冲区可分为输出缓冲区和输出缓冲区，两部分可重叠。在使用缓冲区时要注意对缓冲的刷新，将缓冲区中的数据读/写到某一指定地方。

强制类型转换是通过类型转换运算来实现的。

## 修饰符

### 函数名前

#### extern

声明一个定义在外部的函数。

#### explicit

只用于构造函数，指定构造函数要显式定义，不能隐式转换。

#### constexpr

指示函数返回常量表达式。

### 函数名后

#### 等号

##### =0

只能用于虚函数，表示函数为纯虚函数。

##### =default

只能用于编译器提供默认实现的特殊成员函数，指示使用默认实现。

##### =delete

只能用于编译器提供默认实现的特殊成员函数，指示编译器应该删除该函数的默认实现。

#### 直接修饰

##### const

表示函数不会修改对象（或者说调用期间对象不变），注意不包括mutable修饰的成员变量，可参考本人之前文章：C++中的mutable和volatile。

##### volatile

类似于const修饰的函数，表示对象状态可能随时会改变；const修饰的函数内只能调用自身的const成员方法，同理volatile函数内也只能调用自身volatile成员函数。

##### &

C++11引入的功能，左值引用限定符，指示函数只能被左值对象调用。

##### &&

C++11引入，右值引用限定符，指示函数只能被右值调用。如果函数没有引用限定符修饰，左值和右值均可调用。一个引用限定例子如下。

```
#include <iostream>
struct S {
	void f() & { std::cout << "lvalue\n"; }
	void f() &&{ std::cout << "rvalue\n"; }
};

int main(){
	S s;
	s.f();            // 打印lvalue
	std::move(s).f(); // 打印rvalue
	S().f();          // 打印rvalue
}
```

##### override

C++11引入的功能，声明成员函数覆盖父类的虚函数。声明为override后，子类声明时可不写virtual。

##### final

C++11引入，指示函数是最终实现，子类不应当再定义或覆盖，可与override同时使用。

##### noexcept

C++11引入，修饰函数是否会抛出异常。

##### throw

指示函数抛出异常及类型，C++11起被废弃。

## 二维数组的传递

### 基本规则

可以用二维数组名作为实参或者形参，在被调用函数中对形参数组定义时可以可以指定所有维数的大小，也可以省略第一维的大小说明。如以下的定义是成立的。

```
void Func(int array[3][10]);
void Func(int array[][10]);
```

但是不能把第二维或者更高维的大小省略，如下面的定义是不合法的。

```
void Func(int array[][]);
```

### 特殊方法

可通过以下方式传递维数可变的二维数组。

```
void Func(int **array, int m, int n);
```

在转变后的函数中，`array[i][j]`的表达是错误的，需要转换为以下方式。

```
*((int*)array + n*i + j);
```

例子如下。

```
int a[3][3] = 
{
{1, 1, 1},
{2, 2, 2},
{3, 3, 3}
};
Func((int**)a, 3, 3);
```

## Qt

### 安装

下载以下安装包即可，编译器尽量全选。

```
https://mirrors.tuna.tsinghua.edu.cn/qt/archive/qt/
```

### 集成到VS

打开VS，在管理扩展中搜索`Qt Visual Studio Tools`并安装。安装重启VS后点击扩展，选择`Qt VS Tools`下的`Options`，然后点击`Add`，在`Path`下选择Qt安装目录下的msvc2017_64（或类似编译器），然后点击`OK`即可。

### VS项目打包成exe

在Debug模式下运行程序，生成exe。新建文件夹，名字最好为英文或数字，将项目生成的exe及所需资源文件夹拷贝进去，在文件夹中新建一个txt，输入以下命令。其中C:\QT\5.9.2\msvc2017_64\bin\为qt安装的相关目录，`***.exe`是生成的exe的文件名称。

```
C:\QT\5.9.2\msvc2017_64\bin\windeployqt.exe ***.exe  
pause
```

将文件后缀改为.bat并运行，再把其它需要的ddl复制进去，双击运行exe即可。然后可以用Enigma Virtual Box等工具进行打包。

## gcc环境配置

配置最新的gcc环境可使用万能头文件`<bits/stdc++.h>`。

### 安装

#### Mac

打开终端，安装gcc。

```
brew install gcc
```

然后执行以下命令，注意需先解锁系统分区。

```
cd /usr/bin
sudo rm g++ gcc c++ cpp cc
```

然后进行软连接。其中9.3.0_1会根据所安装的版本不同发生差异，需要自行查看。

```
sudo ln -s /usr/local/Cellar/gcc/9.3.0_1/bin/c++-9 /usr/bin/c++
sudo ln -s /usr/local/Cellar/gcc/9.3.0_1/bin/cpp-9 /usr/bin/cpp
sudo ln -s /usr/local/Cellar/gcc/9.3.0_1/bin/g++-9 /usr/bin/g++
sudo ln -s /usr/local/Cellar/gcc/9.3.0_1/bin/gcc-9 /usr/bin/gcc
```

#### Windows

打开以下链接并下载mingw-get-setup.exe。安装完成后点击左侧的Basic，选择全部安装包并安装即可。

```
https://sourceforge.net/projects/mingw/files/Installer/
```

### 环境配置

#### Sublime

打开Sublime并新建编译系统，复制以下代码后保存为C++.sublime-build，以后选择其为编译系统即可使用。

```
{
    "cmd": ["g++", "${file}" , "-o", "${file_path}/${file_base_name}"],
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "source.c, source.c++",
    "cmd": ["bash", "-c", "g++ '${file}' -o '${file_path}/${file_base_name}' && open -a Terminal.app '${file_path}/${file_base_name}'"],
    "variants":
    [
        {
            "name": "Run",
            "cmd": ["bash", "-c", "g++  '${file}' -std=c++11 -stdlib=libc++ -o '${file_path}/${file_base_name}' && '${file_path}/${file_base_name}'"]
        }
    ]
}
```

#### Clion

进入路径/usr/local/Cellar，将gcc文件夹拷贝到任意目录（如用户目录）。打开CLion，选择`preference`-`build,execution,deployment`-`tool chains`，把C/C++ complier改成刚才复制到的目录里的`gcc-9`/`g++-9`，然后在cMake里填之前的原地址，例子如下。

```
-D CMAKE_CXX_COMPILER=/usr/local/Cellar/gcc/9.3.0_1/bin/g++-9
```

可通过以下代码查看编译器是否为最新版gcc。

```
#include <bits/stdc++.h>
using namespace std;
int main(){
	cout << __VERSION__ << endl;
	return 0;
}
```

## VS配置

VS的主要单元是解决方案（Solution），一个解决方案下可以包含多个项目（Project）。一个项目下可以有多个文件，但只能有一个main函数，VS在编译时需遍历所有文件以寻找main函数。

### 头文件的包含和调用

#### 项目配置复制

将库中文件除debug、x64的文件夹/头文件复制（不要sln文件）。打开项目，在资源中的Header File添加用到的头文件。选择工具栏的`属性`，然后按照以下内容将两个项目的属性复制即可。

```
C/C++-附加包含目录
VC++-库目录
链接器-常规-库目录
链接器-输入-库名称
```

#### 项目配置

打开项目，选择工具栏的`属性`，进入`通用属性`-`C/C++`-`常规中的附加包引项目`，输入头文件所在文件夹。其中`./`为当前项目目录，`../`为上一级。

### 使用万能头文件`<bits/stdc++.h>`

进入路径`C:\Program Files(x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.13.26128\include`（14.13.26128根据版本会有变化），新建stdc++.h，内容如下。

```
// C
#ifndef _GLIBCXX_NO_ASSERT
#include <cassert>
#endif
#include <cctype>
#include <cerrno>
#include <cfloat>
#include <ciso646>
#include <climits>
#include <clocale>
#include <cmath>
#include <csetjmp>
#include <csignal>
#include <cstdarg>
#include <cstddef>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>

#if __cplusplus >= 201103L
#include <ccomplex>
#include <cfenv>
#include <cinttypes>
#include <cstdalign>
#include <cstdbool>
#include <cstdint>
#include <ctgmath>
#include <cwchar>
#include <cwctype>
#endif

// C++
#include <algorithm>
#include <bitset>
#include <complex>
#include <deque>
#include <exception>
#include <fstream>
#include <functional>
#include <iomanip>
#include <ios>
#include <iosfwd>
#include <iostream>
#include <istream>
#include <iterator>
#include <limits>
#include <list>
#include <locale>
#include <map>
#include <memory>
#include <new>
#include <numeric>
#include <ostream>
#include <queue>
#include <set>
#include <sstream>
#include <stack>
#include <stdexcept>
#include <streambuf>
#include <string>
#include <typeinfo>
#include <utility>
#include <valarray>
#include <vector>

#if __cplusplus >= 201103L
#include <array>
#include <atomic>
#include <chrono>
#include <condition_variable>
#include <forward_list>
#include <future>
#include <initializer_list>
#include <mutex>
#include <random>
#include <ratio>
#include <regex>
#include <scoped_allocator>
#include <system_error>
#include <thread>
#include <tuple>
#include <typeindex>
#include <type_traits>
#include <unordered_map>
#include <unordered_set>
#endif
```

### 对话框「发生生成错误，是否继续并运行上次的成功生成」的设置

点击VS菜单栏的`工具`-`选项`-`项目和解决方案`-`生成并运行`-`运行时，当出现生成或部署错误时`，并进行设置即可。

# Arduino

## 教程

```
https://www.w3cschool.cn/arduino/arduino_for_loop.html
```

# 汇编

## 资料

```
https://www.hack520.com/
```

## 调试器

### TASM

```
https://cs.nyu.edu/courses/fall99/V22.0201-002/debug.html
```

# HTML与微信小程序

## HTML教程

```
https://www.w3school.com.cn/html/index.asp
https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Introduction_to_HTML/Getting_started
```

# 数据库系统

## 关系代数计算

```
https://dbis-uibk.github.io/relax/calc.htm
```

## Mysql相关操作

### 安装

#### 下载

从以下网站下载并安装。

```
https://dev.mysql.com/downloads/mysql/
```

#### 配置环境变量

打开用户目录下的`.bash_profile`（zsh为`.zshrc`）文件，在末尾复制以下内容并保存。

```
export PATH=$PATH:/usr/local/mysql/bin
export PATH=$PATH:/usr/local/mysql/support-files
```

打开终端并输入以下命令（zsh为`.zshrc`）。

```
source ~/.bash_profile
```

#### 启动服务

在终端输入以下命令。

```
sudo mysql.server start
sudo mysql.server status
```

### 进入mysql命令行

输入以下命令即可。其中`-u`表示username，`-p`表示password。

```
mysql -u <用户名> -p <密码>
// 或
mysql -u <用户名> -p
```

### 增删改查操作

#### 创建数据库

```
create database <数据库名>;
```

#### 创建新表

```
create table <表名>{
	<键名> <键值> (<属性>),
	(Cno tinyint not null auto_increment primary key,)
	<键名> <键值>,
	...
	<键名> <键值>
};
```

#### 插入数据

注意，如果键的属性为`auto_increment`，则插入数据时可以不用写该值。

```
insert into <表名>(<键名>,...,<键名>) values (<键值>,...,<键值>);
```

#### 更新数据

如果有外码约束，需先消除外码约束。

```
// 取消外码约束
set foreign_key_checks = 0;

// 更新数据
update <表名> set <键名>=<键值> where <条件>;

// 恢复外码约束
set foreign_key_checks = 1;
```

#### 删除数据

如果有外码约束，需先消除外码约束。

```
// 取消外码约束
set foreign_key_checks = 0;

// 更新数据
delete from <表名> where <条件>;

// 恢复外码约束
set foreign_key_checks = 1;
```

### Mac下查看并修改端口号

#### 查询端口号

在mysql命令行输入以下命令即可。

```
show global variables like ‘port’;
```

#### 修改端口号

打开配置文件`/Library/LaunchDaemons/com.oracle.oss.mysql.mysqld.plist`，修改`-port=`后面的数字即可。

### CLion连接到数据库

具体可参照以下链接。

```
https://www.jetbrains.com/help/clion/connecting-to-a-database.html
```

### Mysql连接ODBC

以下操作均在Windows下完成。

#### 安装驱动

在安装MySQL时选中ODBC组件即可。

#### 配置ODBC源

打开控制面板，选择管理工具-ODBC数据源，点击添加，选择MySQL ODBC 8.0 ANSI Driver，按照信息填写即可。

# 计算机网络

## 数字证书与数字签名

```
https://hoochanlon.github.io/fq-book/#/abc/dc_zhenshu
https://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html
```

## 实验

### 阿里云MQTT

#### 使用MQTT客户端连接阿里云MQTT服务器

```
https://yq.aliyun.com/articles/592279
```

#### 使用TinyLink完成MQTT通信

```
http://linklab.tinylink.cn/static/tutorial/tutorial05.html
```

#### 阿里云IoT Studio物模型入门

```
http://linklab.tinylink.cn/static/tutorial/tutorial06.html
```

### GNS3

#### 安装和使用

```
https://github.com/GNS3/gns3-gui/releases
https://github.com/zhang0peter/gns3-intro/
https://blog.csdn.net/zhangpeterx/article/details/86407065
```

#### RIP配置

```
https://jingyan.baidu.com/article/00a07f38043ff782d028dc23.html
https://jingyan.baidu.com/article/a65957f4ec38d224e77f9b4d.html
https://zhidao.baidu.com/question/2051649583868305907.html
```

### 简单Socket连接

#### 服务端

```
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>
#include <sys/types.h>
#include <winsock2.h>
 
#pragma  comment(lib,"ws2_32.lib")
 
#define MAXLINE 4096
 
int main()
{
	SOCKET listenfd;
	int connfd;
	struct sockaddr_in servaddr;
	char buff[MAXLINE + 1];
	int n;
 
	// windows 下需要初始化socket
	WORD sockVersion = MAKEWORD(2, 2);
	WSADATA wsaData;
	if (WSAStartup(sockVersion, &wsaData) != 0)
	{
		printf("init socket error: %s(error: %d)\n", strerror(errno), errno);
		system("pause");
		return 0;
	}
	// end socket初始化
 
	if ((listenfd = socket(AF_INET, SOCK_STREAM, 0)) == -1)
	{
		printf("create socket error: %s(error: %d)\n", strerror(errno), errno);
		system("pause");
		return 0;
	}
	memset(&servaddr, 0, sizeof(servaddr));
	servaddr.sin_family = AF_INET;
	servaddr.sin_addr.s_addr = htonl(INADDR_ANY);
	servaddr.sin_port = htons(6666);
 
	if (bind(listenfd, (struct sockaddr*)&servaddr, sizeof(servaddr)) == -1)
	{
		printf("bind socket error: %s(errno: %d)\n", strerror(errno), errno);
		system("pause");
		return 0;
	}
 
	if (listen(listenfd, 10) == -1)
	{
		printf("listen socket error: %s(errno: %d)\n", strerror(errno), errno);
		system("pause");
		return 0;
	}
 
	printf("======waiting for client's request======\n");
	if ((connfd = accept(listenfd, NULL, NULL)) == -1)
	{
			printf("accept socket error: %s(errno: %d)", strerror(errno), errno);
			system("pause");
			return 0;
	}
	// 接收消息
	while (true)
	{
		memset(buff, 0, sizeof(buff));
		n = recv(connfd, buff, MAXLINE, 0);
		buff[n] = '\0';
		printf("recv msg from client: %s\n", buff);
	}
 
	// 释放socket
	closesocket(connfd);
	closesocket(listenfd);
 
	system("pause");
	return 0;
}
```

#### 客户端

```
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>
#include <sys/types.h>
#include <winsock2.h>
#include <WS2tcpip.h>
 
#pragma  comment(lib,"ws2_32.lib")
 
int main()
{
	int    sockfd, n;
	char   recvline[4096], sendline[4096];
	struct sockaddr_in servaddr;
 
	WORD sockVersion = MAKEWORD(2, 2);
	WSADATA wsaData;
	if (WSAStartup(sockVersion, &wsaData) != 0)
	{
		printf("init socket error: %s(error: %d)\n", strerror(errno), errno);
		system("pause");
		return 0;
	}
 
	printf("please enter service ipv4 address: ");
	char IPV4[20];
	gets(IPV4);
 
	if ((sockfd = socket(AF_INET, SOCK_STREAM, 0)) < 0){
		printf("create socket error: %s(errno: %d)\n", strerror(errno), errno);
		system("pause");
		return 0;
	}
 
	memset(&servaddr, 0, sizeof(servaddr));
	servaddr.sin_family = AF_INET;
	servaddr.sin_port = htons(6666);
 
	if (inet_pton(AF_INET, IPV4, &servaddr.sin_addr) <= 0){
		printf("inet_pton error for %s\n", IPV4);
		system("pause");
		return 0;
	}
 
	if (connect(sockfd, (struct sockaddr*)&servaddr, sizeof(servaddr)) < 0){
		printf("connect error: %s(errno: %d)\n", strerror(errno), errno);
		system("pause");
		return 0;
	}
	while (true)
	{
		printf("send msg to server: \n");
		memset(sendline, 0, sizeof(sendline));
		fgets(sendline, 4096, stdin);
		if (send(sockfd, sendline, strlen(sendline), 0) < 0)
		{
			printf("send msg error: %s(errno: %d)\n", strerror(errno), errno);
			system("pause");
			return 0;
		}
	}
	closesocket(sockfd);
	system("pause");
	return 0;
}
```

## 习题集

### 第一章：引言

```
(1) Imagine that you have trained your St. Bernard, Bernie, to carry a box of three 8mm tapes instead of a flask of brandy. (When your disk fills up, you consider that an emergency.) These tapes each contain 7 gigabytes. The dog can travel to your side, wherever you may be, at 18 km/hour. For what range of distances does Bernie have a higher data rate than a transmission line whose data rate (excluding overhead) is 150 Mbps?

Sol.

7 * 1024 * 8 * 3 / (d / 5) = 150
d = 5734.4m

Wrong!

1 kbps = 1000 bit/s
1024 should be 1000
d = 5600m

(2) An alternative to a LAN is simply a big timesharing system with terminals for all users. Give two advantages of a client-server system using a LAN.

Sol.

(1) 可以更好地利用网络资源，timesharing system容易造成资源浪费
(2) 构建简单，客户机和服务机上各运行一个进程即可

(3) The performance of a client-server system is influenced by two network factors: the bandwidth of the network (how many bits/sec it can transport) and the latency (how many seconds it takes for the first bit to get from the client to the server). Give an example of a network that exhibits high bandwidth and high latency. Then give an example of one with low bandwidth and low latency.

Sol.

(1). 下载大型文件，建立连接需要很久，一旦建立下载很快
(2). 语音通话，不能有高延迟

A transcontinental fiber link might have many gigabits/sec of bandwidth, but the latency will also be high due to the speed of light propagation over thousands of kilometers. In contrast, a 56-kbps modem calling a computer in the same building has low bandwidth and low latency.

(4) Besides bandwidth and latency, what other parameter is needed to give a good characterization of the quality of service offered by a network used for digitized voice traffic?

Sol.

(1) 丢包率
(2) 安全性
(3) 断点传输
(4) 对语音通话而言，统一的传输时间也很重要

(5) A factor in the delay of a store-and-forward packet-switching system is how long it takes to store and forward a packet through a switch. If switching time is 10 μsec, is this likely to be a major factor in the response of a client-server system where the client is in New York and the server is in California? Assume the propagation speed in copper and fiber to be 2/3 the speed of light in vacuum.

Sol.

2000km / 200000km/s = 10ms

It won't be a major factor.

(6) A client-server system uses a satellite network, with the satellite at a height of 40,000 km. What is the best-case delay in response to a request?

Sol.

40,000 * 2 / 300000 = 267ms

(7) In the future, when everyone has a home terminal connected to a computer network, instant public referendums on important pending legislation will become possible. Ultimately, existing legislatures
could be eliminated, to let the will of the people be expressed directly. The positive aspects of such a
direct democracy are fairly obvious; discuss some of the negative aspects.

Sol.

安全性存在问题

(8) A collection of five routers is to be connected in a point-to-point subnet. Between each pair of routers,
the designers may put a high-speed line, a medium-speed line, a low-speed line, or no line. If it takes 100 ms of computer time to generate and inspect each topology, how long will it take to inspect all of them?

Sol.

4 ^ (4 + 3 + 2 + 1) * 100ms = more than one day

(9) A disadvantage of a broadcast subnet is the capacity wasted when multiple hosts attempt to access the channel at the same time. As a simplistic example, suppose that time is divided into discrete slots, with each of the n hosts attempting to use the channel with probability p during each slot. What fraction of the slots are wasted due to collisions?

Sol.

P = 1 - n * p * (1 - p) ^ (n - 1) - p ^ n

(10) What are two reasons for using layered protocols?

Sol.

(1) 使得层与层之间不会影响，一层内的协议更新不会使得整个网络连接发生变化
(2) 把大的任务分细，明确各层提供的服务

(11) The president of the Specialty Paint Corp. gets the idea to work with a local beer brewer to produce an
invisible beer can (as an anti-litter measure). The president tells her legal department to look into it, and they in turn ask engineering for help. As a result, the chief engineer calls his counterpart at the other company to discuss the technical aspects of the project. The engineers then report back to their respective legal departments, which then confer by telephone to arrange the legal aspects. Finally, the two corporate presidents discuss the financial side of the deal. Is this an example of a multilayer protocol in the sense of the OSI model?

Sol.

除了第一层（最底层，这里是engineer），高层之间不能有直接通信。

(12) What is the principal difference between connectionless communication and connection-oriented communication?

Sol.

面向连接的服务是先建立连接再通信。无连接服务是在传输时候带上目标地址，然后交由网络去通信。

(13) Two networks each provide reliable connection-oriented service. One of them offers a reliable byte stream and the other offers a reliable message stream. Are these identical? If so, why is the distinction made? If not, give an example of how they differ.

Sol.

message的话每条信息有确定边界，而byte stream则没有确定边界。

(14) What does ''negotiation'' mean when discussing network protocols? Give an example.

Sol.

协商就是建立一个双方接受的通信规则。

(15) Which of the OSI layers handles each of the following:
a. (a) Dividing the transmitted bit stream into frames.
b. (b) Determining which route through the subnet to use.

Sol.

Transmission Layer and Network Layer

Wrong!

Data link layer and Network layer

这里很重要，数据是在data link层被划分为帧（frame）的

(16) If the unit exchanged at the data link level is called a frame and the unit exchanged at the network level
is called a packet, do frames encapsulate packets or do packets encapsulate frames? Explain your
answer.

Sol.

高层的应该被封到低层里去。

(17) A system has an n-layer protocol hierarchy. Applications generate messages of length M bytes. At each
of the layers, an h-byte header is added. What fraction of the network bandwidth is filled with headers?

Sol.

hn / (M + hn)

(18) List two ways in which the OSI reference model and the TCP/IP reference model are the same. Now list
two ways in which they differ.

Sol.

OSI:
Application
Representation
Session
Transmission
Network
Data link
Physical

TCP/IP:
Application: HTTP, SMTP, RTP, DNS
Transmission: TCP, UDP
internet: IP (Internet Protocal), ICMP
Link: DSL, SONET, 802.11, Ethernet

(19) What is the main difference between TCP and UDP?

Sol.

TCP is connection oriented, reliable, fast.
UDP is connectionless oriented, unreliable, slow.

(20) When a file is transferred between two computers, two acknowledgement strategies are possible. In the first one, the file is chopped up into packets, which are individually acknowledged by the receiver, but the file transfer as a whole is not acknowledged. In the second one, the packets are not acknowledged
individually, but the entire file is acknowledged when it arrives. Discuss these two approaches.

Sol.

If the network is reliable, use the second one.

(21) How long was a bit on the original 802.3 standard in meters? Use a transmission speed of 10 Mbps and
assume the propagation speed in coax is 2/3 the speed of light in vacuum.

Sol.

802.3是以太网，这里计算出来是20米

(22) An image is 1024 x 768 pixels with 3 bytes/pixel. Assume the image is uncompressed. How long does it
take to transmit it over a 56-kbps modem channel? Over a 1-Mbps cable modem? Over a 10-Mbps Ethernet? Over 100-Mbps Ethernet?

Sol.

337s, 19s, 1.9s, 0.19s

(23) Ethernet and wireless networks have some similarities and some differences. One property of Ethernet is that only one frame at a time can be transmitted on an Ethernet. Does 802.11 share this property with Ethernet? Discuss your answer.

Sol.

Wireless networks also transmit one frame at a time, but determining when one can send is more difficult. Ethernet is a broadcast bus so the sender can just listen to see if there is any traffic before sending. Due to range issues with wireless, a sender cannot be sure that there are not other transmissions just because it cannot "hear" other transmissions. Wireless senders use either a central base station or use methods discussed in Chapter 4 to avoid collisions.

(24) Wireless networks are easy to install, which makes them inexpensive since installation costs usually far overshadow equipment costs. Nevertheless, they also have some disadvantages. Name two of them.

Sol.

无法控制范围，容易被人利用。任何人可以接收到传送中的包。
此外，传输更慢。

(25) List two advantages and two disadvantages of having international standards for network protocols.

Sol.

好处：统一的标准使得兼容性更好，成本降低。
坏处：不好的标准可能很难被淘汰，比如OSI比TCP／IP先进但没有被采用。
```

### 第五章：网络层

```
(1) Give two example computer applications for which connection-oriented service is appropriate. Now give two examples for which connectionless service is best.

Sol.

面向连接服务：SSH，文件传输

无连接服务：视频通话，游戏在线对战，需要快速应答的服务一般需要无连接服务。

(2) Are there any circumstances when connection-oriented service will (or at least should) deliver packets out of order? Explain.

Sol.

接收端将无法正确接收数据，比如视频传输，顺序一错视频就乱了。

Wrong!

Circumstance是情况的意思，在terminal中ctrl-c应该被最先传送，而不是排在队尾。

(3) Datagram subnets route each packet as a separate unit, independent of all others. Virtual-circuit subnets do not have to do this, since each data packet follows a predetermined route. Does this observation mean that virtual-circuit subnets do not need the capability to route isolated packets from an arbitrary source to an arbitrary destination? Explain your answer.

Sol.

并不是如此，虚电路网络对不同方向的数据包需要建立不同的虚电路，因此也要具备这样的能力。

补充：在建立虚电路的时候需要把setup package从任意端传输到任意接收方。

(4) Give three examples of protocol parameters that might be negotiated when a connection is set up.

Sol.

IP协议，协商IP包的最大跳数，是否可以分段，还有出错时的处理方式。

(5) Consider the following design problem concerning implementation of virtual-circuit service. If virtual circuits are used internal to the subnet, each data packet must have a 3-byte header and each router must tie up 8 bytes of storage for circuit identification. If datagrams are used internally, 15-byte headers are needed but no router table space is required. Transmission capacity costs 1 cent per 106 bytes, per hop. Very fast router memory can be purchased for 1 cent per byte and is depreciated over two years, assuming a 40-hour business week. The statistically average session runs for 1000 sec, in which time 200 packets are transmitted. The mean packet requires four hops. Which implementation is cheaper, and by how much?

Sol.

Virtual Circuits: 200 * 3 * 4 / 10 ^ 6 + 1000 * 8 * 5 / 2 / 52 / 40 / 3600
Datagrams: 15 * 4 * 200 / 10 ^ 6

(6) Assuming that all routers and hosts are working properly and that all software in both is free of all errors, is there any chance, however small, that a packet will be delivered to the wrong destination?

Sol.

有可能，当有ip冲突或者传输过程中发生ip地址变更的情况。

Wrong!

答案是错误可能出在低层上，比如物理层

(7) Give a simple heuristic for finding two paths through a network from a given source to a given destination that can survive the loss of any communication line (assuming two such paths exist). The routers are considered reliable enough, so it is not necessary to worry about the possibility of router crashes.

Sol.

先找出一条最短路径，再把这条路径删除，找出另一条最短路径。只要两条路径没有公共部分即可。

(8) Consider the subnet of Fig.5-12(a). Distance vector routing is used, and the following vectors have just come in to router C: from B: (5, 0, 8, 12, 6, 2); from D: (16, 12, 6, 0, 9, 10); and from E: (7, 6, 3, 9, 0, 4). The measured delays to B, D, and E, are 6, 3, and 5, respectively. What is C's new routing table? Give both the outgoing line to use and the expected delay.

Sol.

这题是距离矢量算法。

  C 到 A 11 经过 B
  A 11 B
  B 6  B
  C 0  -
  D 3  D
  E 5  E
  F 8  B
(9) If delays are recorded as 8-bit numbers in a 50-router network, and delay vectors are exchanged twice a second, how much bandwidth per (full-duplex) line is chewed up by the distributed routing algorithm? Assume that each router has three lines to other routers.

Sol.

每个路由器需要维护一张400bit的表，因此传输0.5s一次会浪费单个方向800bps的带宽

(10) In Fig. 5-14 the Boolean OR of the two sets of ACF bits are 111 in every row. Is this just an accident here, or does it hold for all subnets under all circumstances?

Sol.

这题讲的是链路状态路由算法。

这个永远是对的，ACK标志标志表示来自哪里，它可能由两条路线过来，而发送标志表示要发送往哪里。

(11) For hierarchical routing with 4800 routers, what region and cluster sizes should be chosen to minimize the size of the routing table for a three-layer hierarchy? A good starting place is the hypothesis that a solution with k clusters of k regions of k routers is close to optimal, which means that k is about the cube root of 4800 (around 16). Use trial and error to check out combinations where all three parameters are in the general vicinity of 16.

Sol.

这题讲的是层次路由。

层次可以分许多层，和网络中路由器的数量规模有关。

16 15 20

(12) In the text it was stated that when a mobile host is not at home, packets sent to its home LAN are intercepted by its home agent on that LAN. For an IP network on an 802.3 LAN, how does the home agent accomplish this interception?

Sol.

家乡代理拥有主机的IP地址即可截获，有什么问题吗？

Conceivably it might go into promiscuous mode, reading all frames dropped onto the LAN, but this is very inefficient. Instead, what is normally done is that the home agent tricks the router into thinking it is the mobile host by re- sponding to ARP requests. When the router gets an IP packet destined for the mobile host, it broadcasts an ARP query asking for the 802.3 MAC-level ad- dress of the machine with that IP address. When the mobile host is not around, the home agent responds to the ARP, so the router associates the mobile user’s IP address with the home agent’s 802.3 MAC-level address.

上面是标准答案，实际上就是在ARP广播时相应家乡代理自己的MAC地址，实际上就是拥有了主机的IP地址嘛。

(13) Looking at the subnet of Fig. 5-6, how many packets are generated by a broadcast from B, using a. (a)reverse path forwarding? b. (b)the sink tree?

Sol.

reverse path forwarding，路由器接收到广播包时，检查是否是自己给广播源发包的路径，是的话说明是从最优路径发过来的，这时给所有其他节点发包，最后算下来发了21次

sink tree是汇集树，是由B到所有节点最短路径的集合，这样发的话发14个包，每个树枝必会到一个节点，而且不会重复。

(14) Suppose that node B in Fig. 5-20 has just rebooted and has no routing information in
its tables. It suddenly needs a route to H. It sends out broadcasts with TTL set to 1, 2,
3, and so on. How many rounds does it take to find a route?

Sol.

这题考查自组织网络路由，就是路由器本身也在移动的情况。TTL分别设置为1，2，3是为了使搜索半径不断增大。由于B和H距离为3，因此TTL设置为3的时候可以发现，因此3轮可以发现路由。

(15) As a possible congestion control mechanism in a subnet using virtual circuits internally,
a router could refrain from acknowledging a received packet until (1) it knows its last transmission along the virtual circuit was received successfully and (2) it has a free buffer. For simplicity, assume that the routers use a stop-and-wait protocol and that each virtual circuit has one buffer dedicated to it for each direction of traffic. If it takes T sec to transmit a packet (data or acknowledgement) and there are n routers on the path, what is the rate at which packets are delivered to the destination host? Assume that transmission errors are rare and that the host-router connection is infinitely fast.

Sol.

讲的是拥塞控制，这个方法不好，需要前一个包完全确认了才能传输下一个包。

The protocol is terrible. Let time be slotted in units of T sec. In slot 1 the source router sends the first packet. At the start of slot 2, the second router has received the packet but cannot acknowledge it yet. At the start of slot 3, the third router has received the packet, but it cannot acknowledge it either, so all the routers behind it are still hanging. The first acknowledgement can only be sent when the destination host takes the packet from the destination router. Now the acknowledgement begins propagating back. It takes two full transits of the network, 2(n − 1)T sec, before the source router can send the second packet. Thus, the throughput is one packet every 2(n − 1)T sec.

(17) Describe two major differences between the ECN and the RED method.

Sol.

这两种是拥塞控制算法。

ECN (Explicit Congestion Notification, 显式拥塞通知)，路由器在它转发的数据包上打上标记，发出信号，接收方注意到拥塞时，发送应答包的同时告知发送方，让发送方降低传送速率。

RED (Random Early Detection, 随机早期检测), 路由器维护一个运行队列长度的平均值，当超过阈值的时候就开始随机丢弃数据包，快速发送方发现丢包就开始降低发送速率。

主要区别是一个显式通知，一个隐式通知，一个是缓存区开始不够了才通知，另一个是提前预知。

(18) An ATM network uses a token bucket scheme for traffic shaping. A new token is put into the bucket every 5 μsec. Each token is good for one cell, which contains 48 bytes of data. What is the maximum sustainable data rate?

Sol.

With a token every 5 μsec, 200,000 cells/sec can be sent. Each packet holds 48 data bytes or 384 bits. The net data rate is then 76.8 Mbps.

(19) A computer on a 6-Mbps network is regulated by a token bucket. The token bucket is filled at a rate of 1 Mbps. It is initially filled to capacity with 8 megabits. How long can the computer transmit at the full 6 Mbps?

Sol.

8 / (6 - 1) = 1.6s

(21) The CPU in a router can process 2 million packets/sec. The load offered to it is 1.5 million packets/sec. If a route from source to destination contains 10 routers, how much time is spent being queued and serviced by the CPUs?

Sol.

这题用到排队论，服务速率为2，到达速率为1.5，那么服务时间为 1／2million / (1 - 1.5 / 2) = 2us。 10个routers就是20us.

(22) Consider the user of differentiated services with expedited forwarding. Is there a guarantee that expedited packets experience a shorter delay than regular packets? Why or why not?

Sol.

不保证，过多包被标记为加速的，那么可能反而变慢了。

(23) Suppose that host A is connected to a router R 1, R 1 is connected to another router, R 2, and R 2 is connected to host B. Suppose that a TCP message that contains 900 bytes of data and 20 bytes of TCP header is passed to the IP code at host A for delivery to B. Show the Total length, Identification, DF, MF, and Fragment offset fields of the IP header in each packet transmitted over the three links. Assume that link A-R1 can support a maximum frame size of 1024 bytes including a 14-byte frame header, link R1-R2 can support a maximum frame size of 512 bytes, including an 8-byte frame header, and link R2-B can support a maximum frame size of 512 bytes including a 12- byte frame header.

Sol.

Link A-R1 :
Length: 900 + 20 (TCP) + 20 (IP) = 940 Bytes, ID:X , DF:0 , MF:0 , Fragment offset:0
Link R1-R2:
(1) Length = 500; ID = x; DF = 0; MF = 1; Offset = 0 (2) Length = 460; ID = x; DF = 0; MF = 0; Offset = 60
Link R2-B:
(1) Length = 500; ID = x; DF = 0; MF = 1; Offset = 0 (2) Length = 460; ID = x; DF = 0; MF = 0; Offset = 60
不用去考虑数据链路层的成帧部分，IP协议不关心。
(24) A router is blasting out IP packets whose total length (data plus header) is 1024 bytes. Assuming that packets live for 10 sec, what is the maximum line speed the router can operate at without danger of cycling through the IP datagram ID number space?

Sol.

IP包的ID字段拥有16位，因此65536个不同编号

65536 ＊ 1024 * 8 / 10 = 54 Gbps

(25) An IP datagram using the Strict source routing option has to be fragmented. Do you think the option is copied into each fragment, or is it sufficient to just put it in the first fragment? Explain your answer.

Sol.

Strict Source Routing 严格源路由，是IPv4头的可选项，表明该包必须经过指定路由。

必须要在每个fragment都要包括。

(26) Suppose that instead of using 16 bits for the network part of a class B address originally, 20 bits had been used. How many class B networks would there have been?

Sol.

B类地址，前缀10定死，因此18bits可以用，所以有2^18个B类网络。

(28) A network on the Internet has a subnet mask of 255.255.240.0. What is the maximum number of hosts it can handle?

Sol.

4096

(29) 为什么以太网地址不能特定于一个网络，而IP地址却可以？

Sol.

Each Ethernet adapter sold in stores comes hardwired with an Ethernet (MAC) address in it. When burning the address into the card, the manufac- turer has no idea where in the world the card will be used, making the address useless for routing. In contrast, IP addresses are either assigned either stati- cally or dynamically by an ISP or company, which knows exactly how to get to the host getting the IP address.

(30) A large number of consecutive IP address are available starting at 198.16.0.0. Suppose that four organizations, A, B, C, and D, request 4000, 2000, 4000, and 8000 addresses, respectively, and in that order. For each of these, give the first IP address assigned, the last IP address assigned, and the mask in the w.x.y.z/s notation.

Sol.

A: 198.16.0.0 – 198.16.15.255 written as 198.16.0.0/20 
B: 198.16.16.0 – 198.23.15.255 written as 198.16.16.0/21 
C: 198.16.32.0 – 198.47.15.255 written as 198.16.32.0/20 
D: 198.16.64.0 – 198.95.15.255 written as 198.16.64.0/19
(31) A router has just received the following new IP addresses: 57.6.96.0/21, 57.6.104.0/21, 57.6.112.0/21, and 57.6.120.0/21. If all of them use the same outgoing line, can they be aggregated? If so, to what? If not, why not?

Sol.

可以聚合成57.6.96.0/19, 这个时候会有57.6.120.0/21没有被聚合，但是有最大匹配原则所以不要紧。

(32) The set of IP addresses from 29.18.0.0 to 19.18.128.255 has been aggregated to 29.18.0.0/17. However, there is a gap of 1024 unassigned addresses from 29.18.60.0 to 29.18.63.255 that are now suddenly assigned to a host using a different outgoing
line. Is it now necessary to split up the aggregate address into its constituent blocks, add the new block to the table, and then see if any reaggregation is possible? If not, what can be done instead?

Sol.

不需要，因为有最长匹配，所以单独聚合即可。

(34) Many companies have a policy of having two (or more) routers connecting the company to the Internet to provide some redundancy in case one of them goes down. Is this policy still possible with NAT? Explain your answer.

Sol.

After NAT is installed, it is crucial that all the packets pertaining to a single connection pass in and out of the company via the same router, since that is where the mapping is kept. If each router has its own IP address and all traffic belonging to a given connection can be sent to the same router, the mapping can be done correctly and multihoming with NAT can be made to work.

所以是可以的，只要网络中的每个主机发给特定router即可。

(36) Describe a way to reassemble IP fragments at the destination.

Sol.

In the general case, the problem is nontrivial. Fragments may arrive out of order and some may be missing. On a retransmission, the datagram may be fragmented in different-sized chunks. Furthermore, the total size is not known until the last fragment arrives. Probably the only way to handle reassembly is to buffer all the pieces until the last fragment arrives and the size is known. Then build a buffer of the right size, and put the fragments into the buffer, maintaining a bit map with 1 bit per 8 bytes to keep track of which bytes are present in the buffer. When all the bits in the bit map are 1, the datagram is complete.

所以就是等尾巴来，就可以确定总长度，然后等所有分段都来就可以重组了。

(37) Most IP datagram reassembly algorithms have a timer to avoid having a lost fragment tie up reassembly buffers forever. Suppose that a datagram is fragmented into four fragments. The first three fragments arrive, but the last one is delayed. Eventually, the timer goes off and the three fragments in the receiver's memory are discarded. A little later, the last fragment stumbles in. What should be done with it?

Sol.

前三段已经被discard了，那么第四段再来会被当成新的，过一段时间一样被扔。

(38) In both IP and ATM, the checksum covers only the header and not the data. Why do you suppose this design was chosen?

Sol.

其他部分的checksum可以交给上层协议，而且开销太大，此外头的错误非常严重。

(39) A person who lives in Boston travels to Minneapolis, taking her portable computer with her. To her surprise, the LAN at her destination in Minneapolis is a wireless IP LAN, so she does not have to plug in. Is it still necessary to go through the entire business with home agents and foreign agents to make e-mail and other traffic arrive correctly?

Sol.

当然需要，无线网是数据链路层和物理层的事情，和IP层无关，还是要利用家乡代理。

(41) The Protocol field used in the IPv4 header is not present in the fixed IPv6 header. Why not?

Sol.

因为在IPv6头中有一个字段叫下一个头，会说明该字段要交给哪一种上层协议处理。

(42) When the IPv6 protocol is introduced, does the ARP protocol have to be changed? If so, are the changes conceptual or technical?

Sol.

不需要做任何改变，只是IP地址需要更长的空间而已。
```

### 第六章：传输层

```
(1) In our example transport primitives of Fig.6-2, LISTEN is a blocking call. Is this strictly necessary? If not, explain how a nonblocking primitive could be used. What advantage would this have over the scheme described in the text?

Sol.

The LISTEN call could indicate a willingness to establish new connections but not block. When an attempt to connect was made, the caller could be given a signal. It would then execute, say, OK or REJECT to accept or reject the con- nection. In our original scheme, this flexibility is lacking.

(2) 像bittorrent这种点对点应用如何区分哪个是connect哪个listen呢？

Sol.

可以竞争，也可以随机，也可以由上层协议控制。

(3) 假设网络层是百分百正确的那么三次握手协议会有什么样的变化？

Sol.

第三次握手就没有必要了，主机2收到主机1的请求时，直接就确立连接，返回给1确认信息后，1也确立连接。

(7) Suppose that the clock-driven scheme for generating initial sequence numbers isused with a 15-bit wide clock counter. The clock ticks once every 100 msec, and the maximum packet lifetime is 60 sec. How often need resynchronization take place
a. (a)in the worst case?
b. (b)when the data consumes 240 sequence numbers/min?

Sol.

首先，现在的规定是这样的，通信的时候初始段的序号等于始终序号，因此在x秒的时候同步的信号序号为x，注意，建立连接之后，序号就和时间无关了！！！！

第二，在时间x时，不允许发送x+T(T是lifetime)序号的段，这是为什么呢？因为你这个段会生存T秒，而在随后的T秒内如果要建立一个连接，可能会和你现在发的这个段序号一样的懂吗？

(a) 题目是这样的，假设在70秒的时候，你建立了一个连接，这个时候你发出去的信号序号为70。注意，之后你要发送的序号是71，然后你一直忍着不发（这就是最坏的情况），然后时间绕了一圈回来了，时间变成了11秒，此时你再想发71，对不起，规则不允许，因为在11秒的时候，不允许发71。这就是最坏情况了，所以必须重新同步一次。答案是3276.8-60=3216.8

(b) 这个时候变成追及问题，最后计算出来结果是5361.3

总结，你发送速率与时钟速率越接近，需要同步的间隔就越长。但是如果一上来超过时钟速率就直接GG。

因为这种方法不好所以后来创造了三次握手，两边的初始序列号都是随机值，就不需要那么麻烦的时钟什么的啦。只要双方确认了，后面都好办。

(9) Imagine that a two-way handshake rather than a three-way handshake were used to
set up connections. In other words, the third message was not required. Are deadlocks
now possible? Give an example or show that none exist.

Sol.

有可能出问题，主机2的确认被延迟了，那么可能会建立起一个重复的连接。

(11) Consider the problem of recovering from host crashes (i.e.,Fig.6-18). If the interval
between writing and sending an acknowledgement, or vice versa, can be made relatively small, what are the two best sender-receiver strategies for minimizing the chance of a protocol failure?

Sol.

If the AW or WA time is small, the events AC(W) and WC(A) are unlikely events. The sender should retransmit in state S1; the receiver’s order does not matter.

(13) Discuss the advantages and disadvantages of credits versus sliding window protocols.

Sol.

滑动窗口协议用于流量控制。

The sliding window is simpler, having only one set of parameters (the win- dow edges) to manage. Furthermore, the problem of a window being increased and then decreased, with the segments arriving in the wrong order, does not occur. However, the credit scheme is more flexible, allowing a dynamic management of the buffering, separate from the acknowledgements.

(14) 拥塞控制的公平性方面的策略，加法递增乘法递减(AIMD).

(15) Why does UDP exist? Would it not have been enough to just let user processes send
raw IP packets?

Sol.

无法确认端口。

(17) A client sends a 128-byte request to a server located 100 km away over a 1-gigabit optical fiber. What is the efficiency of the line during the remote procedure call?

Sol.

发送包需要128*8/1G＝1.024us, 在路上的时间100km/200000 = 50us, 然后一来一回100us，因此利用率约为1%。
所以说这个时候的制约不是带宽而是距离。

(19) Both UDP and TCP use port numbers to identify the destination entity when delivering a message. Give two reasons for why these protocols invented a new abstract ID (port numbers), instead of using process IDs, which already existed when these protocols were designed.

Sol.

进程id动态变化，不易管理，端口号可以被进程绑定，而且一些知名服务需要用固定端口号。

Here are three reasons. First, process IDs are OS-specific. Using process IDs would have made these protocols OS-dependent. Second, a single process may establish multiple channels of communications. A single process ID (per process) as the destination identifier cannot be used to distinguish between these channels. Third, having processes listen on well-known ports is easy, but well-known process IDs are impossible.

(20) 何时选用基于UDP的RPC，合适使用基于TCP的RPC。

Sol.

RPC是远程过程调用，可以建立客户端－服务器应用。如果请求不是幂等的(幂等是如同计数器加一这种命令，执行次数不同会产生不同结果)，那就可以考虑使用UDP。同时，如果传递的数据包并不大，可以考虑使用UDP。

(22) 最小TCP MTU的总长度是多少？包括TCP和IP的开销，但是不包括数据链路层的开销。

Sol.

MTU是最大传输单元，最小的TCP MTU可以设置，如果一台主机不设置的话默认是536+20=556字节的TCP段。Internet要求每台主机至少能够处理556字节的段。

(23) RTP is used to transmit CD-quality audio, which makes a pair of 16-bit samples 44,100 times/sec, one sample for each of the stereo channels. How many packets per second must RTP transmit?

Sol.

Each sample occupies 4 bytes. This gives a total of 256 samples per packet. There are 44,100 samples/sec, so with 256 samples/packet, it takes 44100/256 or 172 packets to transmit one second’s worth of music.

按照标准答案的理解，RTP的一个packet只能传输1024字节，不清楚这个规定是在哪里。

(25) Would it be possible to place the RTP code in the operating system kernel, along with the UDP code? Explain your answer.

Sol.

应该要分开，RTP是基于UDP的协议，其他应用程序也要调用UDP，因此最好可以把两段代码分开。

错了！

Sure. The caller would have to provide all the needed information, but there is no reason RTP could not be in the kernel, just as UDP is.

(26) 主机1的端口p和主机2的端口q之间可能存在多个TCP连接吗？

Sol.

不可能，一对端口之间只能有一个TCP连接。一个进程可以有多个TCP连接。

(27) ACK标志位有什么用？

Sol.

ACK标志位用来表示ACK字段是否有意义。其实在连接已经建立起来之后ACK标志位已经没有意义了，因为ACK是必须的。而在连接建立的过程中，这是非常重要的。

(28) 为什么TCP段的有效载荷是65495字节？

Sol.

因为TCP长度为16位标示，所以最多65535字节，然后去掉TCP头20字节，去掉IP头20字节。剩下65495字节。

(30) Consider the effect of using slow start on a line with a 10-msec round-trip time and no congestion. The receive window is 24 KB and the maximum segment size is 2 KB. How
long does it take before the first full window can be sent?

Sol.

慢速启动是TCP协议中拥塞控制的一个算法，略看。The first bursts contain 2K, 4K, 8K, and 16K bytes, respectively. The next one is 24 KB and occurs after 40 msec.

(31) Suppose that the TCP congestion window is set to 18 KB and a timeout occurs. How big
will the window be if the next four transmission bursts are all successful? Assume that
the maximum segment size is 1 KB.

Sol.

也是拥塞控制的算法，TCP维护一个拥塞窗口，略看。The next transmission will be 1 maximum segment size. Then 2, 4, and 8. So after four successes, it will be 8 KB.

(33) A TCP machine is sending full windows of 65,535 bytes over a 1-Gbps channel that has
a 10-msec one-way delay. What is the maximum throughput achievable? What is the line efficiency?

Sol.

One window can be sent every 20 msec. This gives 50 windows/sec, for a maximum data rate of about 3.3 million bytes/sec. The line efficiency is then 26.4 Mbps/1000 Mbps or 2.6 percent.

因此有延迟的网络传输效率和窗口大小，延迟有很大关系。

(34) What is the fastest line speed at which a host can blast out 1500-byte TCP payloads with a 120-sec maximum packet lifetime without having the sequence numbers wrap around? Take TCP, IP, and Ethernet overhead into consideration. Assume that Ethernet frames may be sent continuously.

Sol.

TCP中每个Byte会占用一个序号，而TCP的sequence number是32位的，所以可以每120s发送2^32Bytes信息，然而1500B信息需要1500+20+20+26(以太网)的段帧来发送因此需要的带宽为2 ^ 32 * 8 * 1566 / 1500 / 120 = 299Mbps。

The goal is to send 2^32 bytes in 120 sec or 35,791,394 payload bytes/sec. This is 23,860 1500-byte frames/sec. The TCP overhead is 20 bytes. The IP overhead is 20 bytes. The Ethernet overhead is 26 bytes. This means that for 1500 bytes of payload, 1566 bytes must be sent. If we are to send 23,860 frames of 1566 bytes every second, we need a line of 299 Mbps. With any- thing faster than this we run the risk of two different TCP segments having the same sequence number at the same time.

(35) 为什么那么多人在为了ipv4的局限性做努力，而对TCP的局限性却没有人这样做。

Sol.

根本原因是IP协议运行在所有路由器上。

IP is a network level protocol while TCP is an end-to-end transport level protocol. Any change in the protocol specification of IP must be incorporated on all routers in the Internet. On the other hand, TCP can works fine as long as the two end points are running compatible versions. Thus, it is possible to have many different versions of TCP running at the same time on different hosts, but not this is not the case with IP.

(36) In a network that has a maximum TPDU size of 128 bytes, a maximum TPDU lifetime of 30 sec, and an 8-bit sequence number, what is the maximum data rate per connection?

Sol.

TPDU:Transport Protocol Data Unit 协议数据单元。

2 ^ 8 * 128 * 8 / 30 = 8.7kbps

(37) Suppose that you are measuring the time to receive a TPDU. When an interrupt occurs,
you read out the system clock in milliseconds. When the TPDU is fully processed, you read out the clock again. You measure 0 msec 270,000 times and 1 msec 730,000 times. How long does it take to receive a TPDU?

Sol.

27次是0ms，73次是1ms，那么平均是730us。

(38) A CPU executes instructions at the rate of 1000 MIPS. Data can be copied 64 bits at a time, with each word copied costing 10 instructions. If an coming packet has to be copied four times, can this system handle a 1-Gbps line? For simplicity, assume that all instructions, even those instructions that read or write memory, run at the full 1000- MIPS rate.

Sol.

1000M * 64 /10 / 4 = 1.6 Gbps > 1Gbps 可以.

(41) For a 1-Gbps network operating over 4000 km, the delay is the limiting factor, not the bandwidth. Consider a MAN with the average source and destination 20 km apart. At what data rate does the round-trip delay due to the speed of light equal the transmission delay for a 1-KB packet?

Sol.

20 * 2 / 200000 = 200us延迟 发送1KB要200us的话，带宽至少要1024 * 8 * 1 / 200u = 40 Mbps

(43) What is the bandwidth-delay product for a 50-Mbps channel on a geostationary satellite? If the packets are all 1500 bytes (including overhead), how big should the window be in packets?

Sol.

The round-trip delay is about 540 msec, so with a 50-Mbps channel the bandwidth-product delay is 27 megabits or 3,375,000 bytes. With packets of 1500 bytes, it takes 2250 packets to fill the pipe, so the window should be at least 2250 packets.
```

# 参考教程

## Windows环境下的安装gcc(c语言环境)

```
https://www.cnblogs.com/fps2tao/p/11539712.html
```