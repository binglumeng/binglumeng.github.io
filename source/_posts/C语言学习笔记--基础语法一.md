---
title: C语言学习笔记--基础语法一
date: 2016-12-15 20:50
tags: 
    - C
categories:
    - 编程相关
---
# C语言学习笔记

C语言是一种通用的、面向过程的计算机编程语言。不同于Java、C#之类的面向对象的语言，C语言适用于底层开发，执行效率接近于汇编语言。

## 基础语法

- 程序结构

  C程序主要包括以下部分

  - 预处理器指令，如`#include <stdio.h>`
  - 函数，如main()
  - 变量
  - 语句&表达式
  - 注释,`//`，`/***/`单行或多行注释

- 基本语法

  C程序由各种令牌组成，`Tokens`，`;`分号结束语句。

  标识符用数字、字母、下划线组成，类似Java，数字不能开头。

  | auto     | else   | long     | switch   |
  | -------- | ------ | -------- | -------- |
  | break    | enum   | register | typedef  |
  | case     | extern | return   | union    |
  | char     | float  | short    | unsigned |
  | const    | for    | signed   | void     |
  | continue | goto   | sizeof   | volatile |
  | default  | if     | static   | while    |
  | do       | int    | struct   | _Packed  |
  | double   |        |          |          |

- 数据类型

  C语言数据类型

  - 基本类型，整数型、浮点型。
  - 枚举类型，也算是算数类型，但是枚举定义了一定的变量数值。
  - void类型，无可用值。
  - 派生类型，包括指针类型、数组类型、结构类型、共用体类型和函数类型。

  整数型`char`、`unsigned char`、`signed char`、`int`、`unsigned int`、`short`、`unsigned short`、`long`、`unsigned long`

  浮点型、`float`、`double`、`long double`之类的。windows下32位、64位系统，变量的值占用字节大小一致，而类Unix下，不完全一致。

  可以用`sizeof()`函数来确定大小范围。

  ```c
  #include <stdio.h>
  #include <limits.h>
  //主函数入口，有时候会写void，int返回类型，都一样。但是不能不寫，而且有的編譯器要求必須是int返回類型
  void main(){
    printf("int 存储大小：%1u \n",sizeof(int));//注意和Java之类的不同。
  }
  ```

  void类型，可以作为返回类型、参数、或者指针的返回指向。

- 变量

  变量定义

  ```C
  //定义变量
  int i,j,k;
  char a;
  float f = 2.2f;
  //声明变量，而不用，在函数外。
  extern value;
  ```

  C语言有左值(Lvalue)和右值(Rvalue)。其实左值就是变量名的意思，右值可理解为具体数值。

- 常量

  整数常量，有十进制、八进制、十六进制，用0x或0X表示十六进制。0表示八进制，整数常量可以带一个后缀，U或者L分辨表示`unsigned`和`long`，也可以小写。

  浮点常量、字符常量。

  | 转义序列     | 含义          |
  | -------- | ----------- |
  | \\\      | \           |
  | \\'      | '           |
  | \\"      | "           |
  | \\?      | ?           |
  | \a       | 警报铃声        |
  | \\b      | 退格键         |
  | \\f      | 换页符         |
  | \\n      | 换行符         |
  | \\r      | 回车          |
  | \\t      | 水平制表符       |
  | \v       | 垂直制表符       |
  | \\ooo    | 一到三位的八进制    |
  | \\xhh... | 一到多位数字的十六进制 |

  char是单引号`''`，字符串是双引号`""`。

  **定义常量**

  - 使用#define
  - 使用const关键字

  ```C
  #include <stdio.h>

  #define LENGTH 10
  #define WIDTH 5
  #define NEWLINE '\n'
  void main(){
    int area;
    
    area = LENGTH * WIDTH;
    printf("value of area: %d",area);
    printf("%c",NEWLINE);
  }
  ```

  const关键字

  ```C
  #include <stdio.h>
  int main(){
    const int LENGTH = 10;
    const int WIDTH = 5;
    const int NEWLINE = '\n';
    ....
    return 0;
  }
  ```

- C存储类

  存储类定义C程序中的变量或函数的范围、可见性和生命周期。

  - auto，存储类是所有==**局部**==变量默认的存储类。

  - register，定义存储在==**寄存器**==而不是ARM中的局部变量，大小通常为一个词，且不能应用一元的`&`运算。比较快访问，但不一定就在寄存器内存储啊，切记。

  - static，存储周期为整个程序的生命周期，它也可以用于修饰全局变量，但是会将变量限制在声明的文件内。有点类似JAVA中的静态。

  - extern，也是提供一个全局变量，会对所有程序文件可见，若是没有初始化，其会自动指向上次存储的数值，也可以修饰函数。

    ```C
    #include <stdio.h>
    int count;
    extern void write_extern();
    void main(){
      count = 5;
      write_extern();
    }
    ```

    ```c
    #include <stdio.h>

    extern int count;
    void write_extern(void){
      printf("count is %d \n",count);
    }
    ```

- C语言运算符

  - 算术运算符：`+`、`-`、`*`、`/`、`%`、`++`、`--`、分别是加减乘除和取模、自增、1、自减1。
  - 关系运算符：`==`、`!=`、`>`、`<`、`>=`、`<=`
  - 逻辑运算符：`&&`、`||`、`!`逻辑与、或、非
  - 位运算符：`&`、`|`、`^`、`~`、`<<`、`>>`，分别是按位与、按位或、按位异或、反转、左移、右移。
  - 赋值运算符：`=`、`+=`、`-=`、`*=`、`/=`、`%=`、`<<=`、`>>=`、`&=`、`^=`、`|=`
  - 杂项运算符：`sizeof()`、`&`、`*`、`?:`分别表示求算大小范围的函数、返回变量的地址、指向一个变量、条件表达式(三目运算符，示例：a>b?x:y，表示如果a>b为真，则取x值，否则取y值。)

  运算符存在优先级，一般可以用`()`括号来改变。

  | 类别      | 运算符                                     | 结合性  |
  | ------- | --------------------------------------- | ---- |
  | 后缀      | () 、[]、 -> 、. 、++、 - -                  | 从左到右 |
  | 一元      | \+ 、\- 、! 、~ 、++、 - -、 (type)*、 &、 sizeof | 从右到左 |
  | 乘除      | *、 / 、%                                 | 从左到右 |
  | 加减      | \+ 、\-                                    | 从左到右 |
  | 移位      | << 、>>                                  | 从左到右 |
  | 关系      | < <= 、> >=                              | 从左到右 |
  | 相等      | ==、 !=                                  | 从左到右 |
  | 位与 AND  | &                                       | 从左到右 |
  | 位异或 XOR | ^                                       | 从左到右 |
  | 位或 OR   | \|                                      | 从左到右 |
  | 逻辑与 AND | &&                                      | 从左到右 |
  | 逻辑或 OR  | \|\|                                    | 从左到右 |
  | 条件      | ?:                                      | 从右到左 |
  | 赋值      | =、 +=、 -=、 *=、 /=、 %=、>>= 、<<=、 &= 、^=  | 从右到左 |
  | 逗号      | ,                                       | 从左到右 |
**Note:**逗号运算符，C++中有描述，用于分隔一系列的运算表达式
```C
int a = 10,b = 12,c = 20;
int d = 0;
//逗号用于分隔不同的运算式，但是赋值，仅区最后一项运算结果
/*
 d = (a++,b--,c+=15);
//此时d值就是c+15 = 35,而同样此时，a值已经++，b值已经--，
printf("d = %d\n",d);
/*
//注释掉上面的赋值运算了，
d = (a++,b--,a+90);
//那么此时，d的值就是101，既，a++,a+90;
```

- 判断与循环

  1、`if`、`if...else`、`if嵌套`、`switch`、`switch嵌套`也可以三目运算符`? : `

  2、`while`、`for`、`do...while`、`嵌套循环`，配合循环控制语句`break`、`continue`、`goto`(控制转移到被标记的语句，一般不建议使用。)；条件为永远为真的循环，为无限循环，程序用应避免这个。还有就是控制条件为空语句，那么就是无限循环了。
```C
#include <stdio.h>
void main(){
	for(;;){
		printf("无限循环中......");
	}
}
```