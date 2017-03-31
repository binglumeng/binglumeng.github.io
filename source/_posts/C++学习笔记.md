---
title: C++学习笔记
date: 2016-12-20 15:28
tags: 
    - C++
categories:
    - 编程相关
---

# C++学习笔记

鉴于C++可看作是C语言的扩展，或者说是面向对象的C语言版本。其语法方面多有类似指出，此笔记也就简要写出C++中与C不同和新增的语法部分。

当然C与C++依然算作是两种语言，此处本人小白，亦不做辩论，只在此方便笔记而已。

## 1、基本语法

- 简介

  linux下C语言用gcc，C++用g++编译；C++含有对象、类、方法、即使变量等概念。

C++ 的预编译库包含了绝大多数C语言的库，自身格式略有不同

```C++
#include <iostream>
using namespace std;//使用标准的命名空间
//C语言是<stdio.h>
int main(){
  cout << "hello C++";//输出语法是cout << ，有时候还要用endl;
}
```

- 关键字

| asm          | else      | new              | this     |
| ------------ | --------- | ---------------- | -------- |
| auto         | enum      | operator         | throw    |
| bool         | explicit  | private          | true     |
| break        | export    | protected        | try      |
| case         | extern    | public           | typedef  |
| catch        | false     | register         | typeid   |
| char         | float     | reinterpret_cast | typename |
| class        | for       | return           | union    |
| const        | friend    | short            | unsigned |
| const_cast   | goto      | signed           | using    |
| continue     | if        | sizeof           | virtual  |
| default      | inline    | static           | void     |
| delete       | int       | static_cast      | volatile |
| do           | long      | struct           | wchar_t  |
| double       | mutable   | switch           | while    |
| dynamic_cast | namespace | template         |          |

- 三字字符

| 三字符组 | 替换   |
| ---- | ---- |
| ??=  | #    |
| ??/  | \    |
| ??'  | ^    |
| ??(  | [    |
| ??)  | ]    |
| ??!  | \|   |
| ??<  | {    |
| ??>  | }    |
| ??-  | ~    |

这些都是古老程序，当时为了表示键盘上没有的字符而设计的。

- 数据类型

  bool、char、int、float、double、void、wchar_t(宽字符型)，以及signed、unsigned、short、long等修饰符

  - **typedef**定义新名称

  ```C++
  typedef int feet;//那么就可以用feet代替int类型定义变量了
  ```

  - 枚举

  ```C++
  enum enum_name{list of names} var-list;
  //示例
  enum color{red,green,blue} c;
  c = blue;
  //默认情况，第一个值=0，第二个=1，依次+1，也可以直接定义
  enum shape {rectangle,circle=5,square} sp;
  //那么此时，square=6，比上一个+1.
  ```

  const声明常量，成员变量自动初始化，char=‘\0';默认初始值

  有符号和无符号的修饰符

  ```C++
  #include　<iostream>
  using namespace std;
  int　main(){
    short int i;//有符号短整数
    unsigned short int j;//无符号的
    j = 50000;//赋值
    i = j;
    cout << i << " , " << j;
    return 0;
  }
  //输出结果
  -15536 , 50000
  ```

- C++限定符

  - const 常量修饰符
  - volatile 告知编译器，其修饰的变量可能会被未知方式修改
  - restrict修饰的指针，是一种唯一访问它所修饰对象的方式

- C++存储类

  auto、register、static、extern、mutable(用于对象修饰，允许对象成员代替常量，mutable成员可通过const成员修改)

- String类

  相比char字符类型，增加了许多常用便捷操作

- 引用

  引用不同于指针

  - 没有空引用，可以用空指针
  - 一旦引用被初始化为一个对象，就不能指向其他对象，指针可以随时变向
  - 引用必须创建时初始化，指针可以随时

  引用使用`&`符号

  ```C++
  //变量的声明初始化
  int i = 17;
  //声明变量i的引用
  int& r = i;//成为r是初始化为i的整形引用
  ```

- 基本输入输出

  I/O库

  ```C++
  <iostream>、<iomanip>、<fstream>等
  ```

  - 标准输出cout，输入cin

  ```C++
  #include <iostream>
  using namespace std;
  int main(){
    char str[] = "Hello C++";
    cout << "Value of Str is : " << str << endl;
    char name[30];
    cout << "Please input your name";
    cin >> name;
    cout << "your name is : "<<name<<endl;//endl  --  end line;
  }
  ```

  - 标准错误流cerr，日志流clog

## 2、面向对象

- 对象类

  **class**关键字

  ```C++
  class Box {
    public ://还有private、protected，friend有点不同
    	double length;
    	double breadth;
    	double height;
  };
  //声明类对象
  Box box1;
  //然后用.符号访问类成员
  box1.length
  ```

- 继承

  C++可以多继承

  ```C++
  //格式
  class derived-class : access-specifier base-class
  //示例
  class Rectangle : public Shape{
    ...
  }
  ```

  权限修饰符public、private、protected

  | 访问   | public | protected | private |
  | ---- | ------ | --------- | ------- |
  | 同一个类 | yes    | yes       | yes     |
  | 派生类  | yes    | yes       | no      |
  | 外部类  | yes    | no        | no      |

  派生类可以继承所有基类的方法，例外

  - 基类的构造函数、析构函数和拷贝构造函数
  - 积累的重载运算符(就是重新定义基本的运算符)
  - 基类的友元函数(friend修饰的函数)

  **注意**一般继承类型选择public，少用private和protected，因为使用对应的修饰符，基类的成员在派生类中将变成对应修饰符的权限级别。如使用private，则基类的public和protected成员，在派生类也会private化。

  ```C++
  class <sub-name>:<visit><base1>,<visit><base2>
    {
      ...
    };
  //示例
  class Rectangle:public Shape,public PaintCost{
    ...
  };
  ```

- 重载运算符和重载函数

  函数重载也就是Java之类的面向对象里的方法重载，既同一个方法名，不同的方法签名。

  - 运算符重载

  ```C++
  Box operator+(const Box&);
  //格式如上，必须有operator关键字，内部传入引用
  //如此box1+box2就被如下重载定义
  Box operator+(const Box& b){
    Box box;
    box.length = this->length + b.length;
    ...
  }
  ```

   下面是可重载的运算符列表：

  | +    | -    | *    | /      | %      | ^         |
  | ---- | ---- | ---- | ------ | ------ | --------- |
  | &    | \|   | ~    | !      | ,      | =         |
  | <    | >    | <=   | \>=     | ++     | --        |
  | <<   | \>>   | ==   | !=     | &&     | \|\|      |
  | +=   | -=   | /=   | %=     | ^=     | &=        |
  | \|=  | *=   | <<=  | \>>=    | []     | ()        |
  | ->   | ->*  | new  | new [] | delete | delete [] |

  下面是不可重载的运算符列表：

  `::`、`.*`、`.`、`?:`

- 多态

  面向对象编程语言三大特性，封装、继承、多态。

  所谓多态，也就是一个方法或属性，在不同的派生类那里，有不同的具体表现方式。

  但是C++语言里，需要派生类实现的抽象方法，需要使用关键字`virtual`在基类中修饰，不然的话，子类调用的仍然还是父类方法，而不是自己的具体实现：

  ```C++
  class Shape{
    ...
    public:
    ...
    //此处若不用virtual修饰，那么子类实现该方法，调用时候，就依然是该方法，而不是子类自己的实现
    virtual int area(){
  	cout << "Parent class area:"<<endl;
      return 0;
    }
  }
  //示例子类
  class Circle{
    ...
    public :
    ...
    int area(){
      cout << "Circle class area:" << end;
      return 0;
    }
  }
  int main(){
    Shape *shape;
    Circle circle;
    //存储circle对象地址
    shape = circle;
    //调用,如果Shape类area()函数不用virtual的修饰，那么输出的只能是Shape类的方法，而不是子类Cirle的area()函数。
    shape->area();
    return 0;
  }
  ```

  使用virtual修饰的函数成为虚函数，如果虚函数没有方法体，则成为纯虚函数。

  类似于Java中的抽象方法。

- 数据抽象与数据封装

  数据抽象就是私有化数据变量，推外提供set、get方法。数据封装，也就是类似于C中的结构体，或者java中的bean对象类，封装一组数据和函数，成为一个实体类。

  **抽象类**一个含有纯虚函数的类。也称为接口。

## 3、高级教程
- 文件和流

`iostream`标准库，提供了`cin`、`cout`方法，而文件标准库`fstream`提供了三种数据类型
`ofstream`、`ifstream`、`fstream`分别对应的是输出、输入、和同时具备输入输出的类型。

- 打开文件
```C++
//参数表示，文件路径和名称，读取方式
void open(const char *filename,ios::openmode mode);
```
|模式标志|描述|
|--|--|
|ios::app|追加模式|
|ios::ate|打开文件，定位到末尾|
|ios::in|读取|
|ios::out|写入|
|ios::trunc|若文件存在，打开前截断为0字节|

这些模式可以结合使用，`::`符号为**域**定位符号
```C++
ofstream outfile;
outfile.open("file.dat",ios::out | ios::trunc);
```
- 关闭文件

`close()`函数是fstream、ifsteram、ofstream对象的一个成员。
```C++
#include <fstream>
#include <iostream>
using namespace std;
 
int main ()
{
    
   char data[100];

   // 以写模式打开文件
   ofstream outfile;
   outfile.open("afile.dat");

   cout << "Writing to the file" << endl;
   cout << "Enter your name: "; 
   cin.getline(data, 100);

   // 向文件写入用户输入的数据
   outfile << data << endl;

   cout << "Enter your age: "; 
   cin >> data;
   cin.ignore();
   
   // 再次向文件写入用户输入的数据
   outfile << data << endl;

   // 关闭打开的文件
   outfile.close();

   // 以读模式打开文件
   ifstream infile; 
   infile.open("afile.dat"); 
 
   cout << "Reading from the file" << endl; 
   infile >> data; 

   // 在屏幕上写入数据
   cout << data << endl;
   
   // 再次从文件读取数据，并显示它
   infile >> data; 
   cout << data << endl; 

   // 关闭打开的文件
   infile.close();

   return 0;
}
```

- 文件位置指针

istream和ostream都提供了对文件重新定位的函数功能。这些成员函数包括关于 istream 的 seekg（"seek get"）和关于 ostream 的 seekp（"seek put"）。
seekg 和 seekp 的参数通常是一个长整型。第二个参数可以用于指定查找方向。查找方向可以是 ios::beg（默认的，从流的开头开始定位），也可以是 ios::cur（从流的当前位置开始定位），也可以是 ios::end（从流的末尾开始定位）。
```c++
// 定位到 fileObject 的第 n 个字节（假设是 ios::beg）
fileObject.seekg( n );

// 把文件的读指针从 fileObject 当前位置向后移 n 个字节
fileObject.seekg( n, ios::cur );

// 把文件的读指针从 fileObject 末尾往回移 n 个字节
fileObject.seekg( n, ios::end );

// 定位到 fileObject 的末尾
fileObject.seekg( 0, ios::end );
```

- 异常处理
try...catch ，throw,好像C++中没有throws关键字

| 异常 | 描述 |
|--|--|
| **std::exception** | 该异常是所有标准 C++ 异常的父类。 |
| std::bad_alloc | 该异常可以通过 **new** 抛出。 |
| std::bad_cast | 该异常可以通过 **dynamic_cast** 抛出。 |
| std::bad_exception | 这在处理 C++ 程序中无法预期的异常时非常有用。 |
| std::bad_typeid | 该异常可以通过 **typeid** 抛出。 |
| **std::logic_error** | 理论上可以通过读取代码来检测到的异常。 |
| std::domain_error | 当使用了一个无效的数学域时，会抛出该异常。 |
| std::invalid_argument | 当使用了无效的参数时，会抛出该异常。 |
| std::length_error | 当创建了太长的 std::string 时，会抛出该异常。 |
| std::out_of_range | 该异常可以通过方法抛出，例如 std::vector 和 std::bitset<>::operator[]()。 |
| **std::runtime_error** | 理论上不可以通过读取代码来检测到的异常。 |
| std::overflow_error | 当发生数学上溢时，会抛出该异常。 |
| std::range_error | 当尝试存储超出范围的值时，会抛出该异常。 |
| std::underflow_error | 当发生数学下溢时，会抛出该异常。 |
也可以通过继承exception类，重载其`what()`方法，来自定义异常类
```C++
#include <iostream>
#include <exception>
using namespace std;

struct MyException : public exception
{
  const char * what () const throw ()
  {
    return "C++ Exception";
  }
};
 
int main()
{
  try
  {
    throw MyException();
  }
  catch(MyException& e)
  {
    std::cout << "MyException caught" << std::endl;
    std::cout << e.what() << std::endl;
  }
  catch(std::exception& e)
  {
    //其他的错误
  }
}
```

- 动态内存
    - 堆，程序中未使用，运行时动态分配的内存

    - 栈，函数内部声明的变量，都在此。

new 和 delete 运算符，用于动态分配内存。
`new data-type`
```C++
double* pvalue = NULL;//初始化为null的指针
pvalue = new double;//为变量请求内存，但是可能不一定有内存可分配，需要检查
if(!(pvalue = new double)){
  cout << "Error : out of memory." << endl;
  exit(1);
}
```
C 语言中有`malloc()`函数，但是C++ 最好用new，因为它可以分配内存，而且还创建了对象。
```C++
delete pvalue;//释放pvalue所指向的内存。
//如下示例
#include <iostream>
using namespace std;

int main ()
{
   double* pvalue  = NULL; // 初始化为 null 的指针
   pvalue  = new double;   // 为变量请求内存
 
   *pvalue = 29494.99;     // 在分配的地址存储值
   cout << "Value of pvalue : " << *pvalue << endl;

   delete pvalue;         // 释放内存

   return 0;
}
```
- 数组动态分配内存
```C++
char* pvalue = NULL;//初始化指针为null
pvalue = new char[20];//请求内存
delete [] pvalue;//删除pvalue指向的数组

int ROW = 2;
int COL = 3;
double **pvalue = new double* [ROW];//为行分配内存
//为列分配内存
for(int i = 0;i<COL;i++){
  pvalue[i] = new double[COL];
}
//释放内存
for(int i = 0;i<COL;i++){
  delete[] pvalue[i];
}
delete [] pvalue;
```
- 对象的动态分配内存
类似普通数据的动态分配：
```c++
#include <iostream>
using namespace std;
class Box{
  public :
  //创建对象时候调用
    Box(){
      cout<<"构造函数"<<endl;
    }
    //删除对象时候调用
    ~Box(){
      cout<<"析构函数"<<endl;
    }
};
int main(){
  //声明分配内存
  Box* myBoxArray = new Box[4];
  //释放内存
  delete [] myBoxArray;
  return 0;
}
```
- 命名空间
有点类似包名，或者xml中的命名空间的概念，就是通过一个完整路径定位，来便于区分不同地方的相同名称的函数和类。
关键字`namespace`
```c++
namespace namespace_name{
  //代码声明
}

//调用的时候
name::code;//code代表函数，或者变量
//需要在文件头使用using namespace指令来说明使用的是哪个空间
//类似于导入包，可以导入单个类，也可以导入整个包,代码中直接使用某个函数，如下
using std::cout;
```
命名空间可以定义在不同的文件中，成为不连续的命名空间。还可以嵌套。
调用时候用`::`定位符
```c++
using namespace name1::name2;
```

- 模板
c++ 的模板也就是泛型，可以这么理解
```c++
template <class type> ret-type func-name(parameter list){
  //body code
}
```
示例：
```c++
#include <iostream>
#include <string>

using namespace std;

template <typename T>
inline T const& Max (T const& a, T const& b) 
{ 
    return a < b ? b:a; 
} 
int main ()
{
 
    int i = 39;
    int j = 20;
    cout << "Max(i, j): " << Max(i, j) << endl; 

    double f1 = 13.5; 
    double f2 = 20.7; 
    cout << "Max(f1, f2): " << Max(f1, f2) << endl; 

    string s1 = "Hello"; 
    string s2 = "World"; 
    cout << "Max(s1, s2): " << Max(s1, s2) << endl; 

   return 0;
}
```
以上是函数模板，类模板也差不多
```C++
template <class type> class class-name {
.
.
.
}
```

- 信号处理
C++ 中多了信号处理,在<csignal>文件中

| 信号 | 描述 |
|--|--|
| SIGABRT | 程序的异常终止，如调用 **abort**。 |
| SIGFPE | 错误的算术运算，比如除以零或导致溢出的操作。 |
| SIGILL | 检测非法指令。 |
| SIGINT | 接收到交互注意信号。 |
| SIGSEGV | 非法访问内存。 |
| SIGTERM | 发送到程序的终止请求。 |
  
- signal()函数
```c++
void (*signal (int sig,void (*func)(int)))(int);
//第一个参数证书，代表信号编号，第二个是指针，指向信号处理函数。
//示例：
#include <iostream>
#include <csignal>

using namespace std;

void signalHandler( int signum )
{
    cout << "Interrupt signal (" << signum << ") received.\n";

    // 清理并关闭
    // 终止程序  

   exit(signum);  

}

int main ()
{
    // 注册信号 SIGINT 和信号处理程序
    signal(SIGINT, signalHandler);  

    while(1){
       cout << "Going to sleep...." << endl;
       sleep(1);
    }

    return 0;
}
//此时，运行循环，用ctrl c来终端，就会被捕获信号。
```
raise()函数，生成信号
`int raise(signal sig);`
//sig表示信号编号，
```C++
#include <iostream>
#include <csignal>

using namespace std;

void signalHandler( int signum )
{
    cout << "Interrupt signal (" << signum << ") received.\n";

    // 清理并关闭
    // 终止程序 

   exit(signum);  

}

int main ()
{
    int i = 0;
    // 注册信号 SIGINT 和信号处理程序
    signal(SIGINT, signalHandler);  

    while(++i){
       cout << "Going to sleep...." << endl;
       if( i == 3 ){
          raise( SIGINT);
       }
       sleep(1);
    }

    return 0;
}
```

- 多线程
多任务计算机分为两种，基于进程和基于线程。基于进程的多任务是程序的并发执行；基于线程的多任务是同一程序的片段并发执行。
常见操作系统多是用POSIX编写线程。
1、创建
```c++
#include <pthread.h>
pthread_create (thread,attr,start_routine,arg);
//参数，thread为指向线程标识符的指针，attr为不透明的属性对象，用来设置线程属性，可以设置NULL
//start_routine线程运行起始位置，创建线程就被执行。arg运行函数的参数，
//可以NULL，但是必须把引用作为指针转换为void类型传入。

//终止线程
pthread_exit(status);//一般不用显式调用，
```

实例：
```c++
#include <iostream>
// 必须的头文件是
#include <pthread.h>

using namespace std;

#define NUM_THREADS 5

// 线程的运行函数
void* say_hello(void* args)
{
    cout << "Hello Runoob！" << endl;
}

int main()
{
    // 定义线程的 id 变量，多个变量使用数组
    pthread_t tids[NUM_THREADS];
    for(int i = 0; i < NUM_THREADS; ++i)
    {
        //参数依次是：创建的线程id，线程参数，调用的函数，传入的函数参数
        int ret = pthread_create(&tids[i], NULL, say_hello, NULL);
        if (ret != 0)
        {
           cout << "pthread_create error: error_code=" << ret << endl;
        }
    }
    //等各个线程退出后，进程才结束，否则进程强制结束了，线程可能还没反应过来；
    pthread_exit(NULL);
}
```
连接和分离线程
```c++
pthread_join(threadid,status)
pthread_detach(threadid)

#include <iostream>
#include <cstdlib>
#include <pthread.h>
#include <unistd.h>

using namespace std;

#define NUM_THREADS     5

void *wait(void *t)
{
   int i;
   long tid;

   tid = (long)t;

   sleep(1);
   cout << "Sleeping in thread " << endl;
   cout << "Thread with id : " << tid << "  ...exiting " << endl;
   pthread_exit(NULL);
}

int main ()
{
   int rc;
   int i;
   pthread_t threads[NUM_THREADS];
   pthread_attr_t attr;
   void *status;

   // 初始化并设置线程为可连接的（joinable）
   pthread_attr_init(&attr);
   pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);

   for( i=0; i < NUM_THREADS; i++ ){
      cout << "main() : creating thread, " << i << endl;
      rc = pthread_create(&threads[i], NULL, wait, (void *)i );
      if (rc){
         cout << "Error:unable to create thread," << rc << endl;
         exit(-1);
      }
   }

   // 删除属性，并等待其他线程
   pthread_attr_destroy(&attr);
   for( i=0; i < NUM_THREADS; i++ ){
      rc = pthread_join(threads[i], &status);
      if (rc){
         cout << "Error:unable to join," << rc << endl;
         exit(-1);
      }
      cout << "Main: completed thread id :" << i ;
      cout << "  exiting with status :" << status << endl;
   }

   cout << "Main: program exiting." << endl;
   pthread_exit(NULL);
}
```
//效果
```c++
main() : creating thread, 0
main() : creating thread, 1
main() : creating thread, 2
main() : creating thread, 3
main() : creating thread, 4
Sleeping in thread 
Thread with id : 4  ...exiting 
Sleeping in thread 
Thread with id : 3  ...exiting 
Sleeping in thread 
Thread with id : 2  ...exiting 
Sleeping in thread 
Thread with id : 1  ...exiting 
Sleeping in thread 
Thread with id : 0  ...exiting 
Main: completed thread id :0  exiting with status :0
Main: completed thread id :1  exiting with status :0
Main: completed thread id :2  exiting with status :0
Main: completed thread id :3  exiting with status :0
Main: completed thread id :4  exiting with status :0
Main: program exiting.
```
