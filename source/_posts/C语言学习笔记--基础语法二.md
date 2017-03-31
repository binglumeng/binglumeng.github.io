---
title: C语言学习笔记--基础语法二
date: 2016-12-17 21:50
tags: 
    - C
categories:
    - 编程相关
---
# C语言基础部分二

## 1、函数

函数，可以称为方法、子例程或程序等等。定义一个函数需要**声明**函数名称、返回类型和参数。函数就是一组执行特定任务和逻辑的代码语句。C程序至少有一个函数---main();
**main函數要有返回值，void類型或者int，float等，有的編譯器要求必須int，需注意這點，有的竟然还可以不写返回类型，编译器标准不同，c89,c99,c11之类的，好吧，最好写上吧。**

- 定义函数

  ```C
  return_type function_name(parameter list){
    body of the function;
  }
  ```

  函数的构成：

  - 返回类型，若有则是返回数据的数据类型，若无则是void，或者不写。
  - 函数名称，也就是函数的实际名，与参数列表一起构成函数签名。
  - 参数，参数类似与占位符，用于接收传递过来的参数，在函数内部调用使用，参数可选，函数可无参数。
  - 函数主体，也就是执行一定的任务逻辑的代码语句。

  ```C
  /*
   * 函数返回两个数字中较大的那个数。
   */
  int max(int num1,int num2){
    //声明局部变量
    int result;
    if(num1>num2){
      result = num1;
    }esle{
      result = num2;
    }
    return result;
  }
  ```

  **Note:**函数声明包含返回类型、函数名、参数列表。而不必有函数主体。

  C语言中，函数声明参数列表中的参数名并不重要，只要有参数类型亦可。

  ```C
  //如此也是合法的
  int max(int,int);
  ```

  函数参数多类似为形式参数，调用者需要传递实际参数。

  | 调用类型 | 描述                                       |
  | ---- | ---------------------------------------- |
  | 传值调用 | 调用方将数值传递给形参，如果形参在函数内修改数值，但不会形象到外部的实际参数值。C 语言多用此方式。 |
  | 引用调用 | 调用方将参数的地址复制给形参，此时在函数内形参修改数值，会同步影响到外部实际参数的。 |
  | 指针形式 | 通过指针传递的方式，形参为实参的指针，操作会影响到实际参数的数据。        |

- 作用域

  任何一个编程语言中，定义的变量都应有其作用范围，称为作用域

  - 局部变量，作用与函数内。
  - 全局变量，作用与函数外部所有范围。
  - 形式参数，在函数的参数定义中，也就在函数内有效。类似局部变量了。

  **C语言中，函数的局部参数可以和全局参数同名称，但是内部仅使用局部变量的值。局部变量需要手动初始化，全局变量会自动初始化。**

- 数组

  C语言数组和Java的数组类似，用于存储一类相同类型的数值，声明一个变量，用序列编号指定元素数据。

  | 元素         | 元素         | 元素     | 元素         |
  | ---------- | ---------- | ------ | ---------- |
  | numbers[0] | numbers[1] | ...... | numbers[n] |

  声明与初始化：

```C
  //声明
  type arrayName[arraySize]
  double balance[10];
  double balance[3] = {12.0,3.5,8.8};
  double balance[] = {7.9,0.1,3.2}
  //单个元素赋值
  balacne[0] = 8.9;
```

  数组的访问使用数组下标，从左至右依次为0----n，某个元素则为arrayName[i];

  | 概念      | 描述                                   |
  | ------- | ------------------------------------ |
  | 多维数组    | 即数组的元素又是数组，常见的为二维数组。                 |
  | 传递数组函数  | 通过指定不带索引的数组名称，来给函数传递一个数组的指针。也就是作为形参。 |
  | 从函数返回数组 | 函数的返回类型。                             |
  | 指向数组的指针 | 通过指定不带索引的数组名称，来生成一个指向数组中第一个元素的指针。    |

## 2、指针

C语言指针是简单而有趣的一个概念，便于简化程序任务，比如动态内存分配等。

每一个变量都有一个内存地址，这个地址是有一个编号的。

```C
#include <stdio.h>
void main(){
  int var1;
  char var2[10];
  //printf函数参数为可变参数，必有string哦，不然会报错。和java不同。
  printf("var1变量的地址：%x \n",&var1);
  printf("varsb变量的地址：%x \n",&var2);
}
```

- 指针

  是一个特殊变量，它的值是另一个变量的地址，内存位置的直接地址。类似其他变量和常量，在使用指针存储其他变量的地址之前，需要声明和存储。

  ```C
  //type为指针的基类型，var-name为指针变量的名称。星号用于标示一个变量是指针。
  type *var-name;
  //指针示例
  int *ip;
  double *dp;
  float *fp;
  char *cp;
  //不管基类型为何种类型，指针的实际数据类型，都是一个代表内存地址的十六进制数。
  ```

- 如何使用指针

  使用指针涉及以下操作：

  - 定义指针变量
  - 把另一变量地址赋值给指针
  - 访问指针变量中可用地址的值

  `*`用来返回位于操作数所指定的地址的变量的值。

  ```c
  #include <stdio.h>
  void main(){
    int var  = 20;//实际变量的声明
    int *ip;//指针变量的声明
    
    ip = &var;//在指针变量中存储var变量的地址，也就是给指针变量赋值
    printf("var变量的地址：%x\n",&var);//C语言printf函数不会换行，所以习惯都是内部加上\n，初学者可别弄混了哦。
    //在指针变量中存储的地址
    printf("在ip中存储的地址：%x \n",ip);
    //使用指针方位实际变量的值，也就用指针指向的内存地址，读取地址里面的数值。
    printf("指针所指向地址的存储数据：%d \n",*ip);//使用*ip格式来获取指针对应的实际数值。
  }
  ```

  **使用\*符号来作用与指针，便可得到指针所指向地址内的实际数值。**

- Null指针

  变量声明的时候，如果没有明确赋值，最好给指针赋值一个NULL值，如此为空指针。

  `NULL`指针是定义在标准库中值为零的常量。

  ```C
  #include <stdio.h>
  void main(){
    int *ip=null;
    //输出ip的值，就是0
    printf("ip的值是：%x \n",ip);
  }
  ```

  **Note:**弄清几个概念，&var表示var的地址，*var是指var指针所指向地址的储存的数值。

  多数操作系统都不允许访问地址为0的内存，其为系统保留内存，有特殊含义，表明指针不指向可访问的内存位置。依据惯例指针包含空值，则认为它不指向任何地址。

  ```c
  if(ptr)//表示，如果ptr非空，则完成
  if(!ptr)//表示，如果ptr为空，则完成。
  ```

- 指针详解

  | 概念      | 描述                           |
  | ------- | ---------------------------- |
  | 指针的算数运算 | 指针可以进行四种算术运算++、--、+、-        |
  | 指针数组    | 可以定义用来存储指针的数组                |
  | 指向指针的指针 | C语言允许指向指针的指针                 |
  | 传递指针给函数 | 通过引用过地址传递参数，使传递的参数在调用的函数中被改变 |
  | 从函数返回指针 | C允许函数返回指针到局部变量、静态变量和动态内存分配   |


- 字符串

  C语言中字符串实际上是使用`null`字符`\0`终止的一维字符数组。
```C
  char cs[6] = {'H','e','l','l','o','\0'};
  //这就是hello字符串，用的是null字符'\0'结尾的一个字符数组,所以数组长度比字符串数字多1,因为末尾是null的字符标记。也可以写作：
  char cs[] = "Hello";
```

  C/C++中定义的字符串的内存表示：

 ![string](http://img.blog.csdn.net/20161217213357762?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  其实可以不必写出null标记，C编译器会自动追加，在初始化时候。

  常用的C语言字符串操作函数：

  | 函数            | 描述                                   |
  | ------------- | ------------------------------------ |
  | strcpy(s1,s2) | 复制字符串s2到s1                           |
  | strcat(s1,s2) | 连接字符串s2到s1末尾                         |
  | strlen(s1)    | 返回字符串s1的长度                           |
  | strcmp(s1,s2) | s1==s2则返回0,`s1<s2`则返回小于0,s1>s2则返回大于0 |
  | strchr(s1,ch) | 返回一个指针，指向字符串s1中字符ch的第一次出现的位置         |
  | strstr(s1,s2) | 返回一直指针，指向字符串s1中字符串s2的第一次出现的位置        |

## 3、结构体

C语言中数组是存储一类相同类型的数据变量，而`结构`则可以存储不同类型的数据项。有点类似面向对象的编程语言中的实体类`bean`的封装。

- 定义结构

  `struct`语句，可以定义一个包含多个成员的数据类型。

  ```C
  struct [structure tag]{
    member definition;
    member definition;
    ...
    member definition;
  }[one or more structure variables]
  ```

  其中`structure tag `是可选的，每个`member definition`是标准的变量定义，比如`int i;double d;`如下示例，定义一个Book结构的方式

  ```C
  struct Books{
    char title[50];
    char author[50];
    char subject[100];
    int book_id;
  } book;
  ```

- 访问结构成员

  使用成员运算符`.`示例：

  ```C
  #include <stdio.h>
  #include <string.h>
  //定义结构体
  struct Books{
      char title[50];
  	char author[50];
  	char subject[100];
  	int book_id;
  };
  //main函数
  void main(){
    //声明结构体的变量，使用关键词struct
    struct Books Book1;
    struct Books Book2;
    //初始化变量的内部数据,结构体成员，用变量名.成员名
    strcpy(Book1.title,"C语言编程");
    strcpy(Book1.author,"谭浩强");
    strcpy(Book1.subject,"C语言编程入门");
    Book1.book_id = 10086;
    //输出Book1信息
    printf("Book1的标题：%s \n",Book1.title);
    printf("Book1的作者：%s \n",Book1.author);
    printf("Book1的副标题：%s \n",Book1.subject);
    printf("Book1的编号：%d \n",Book1.book_id);
    //Book2类似
    ...
  }
  ```

- 结构体作为函数参数

  结构体也可以作为参数传递给函数调用，类似变量和指针。不同与Java语言的方法函数，这里需要先声明结构函数，然后实现，在调用。

  ```c
  #include <stdio.h>
  #include <string.h>
   
  struct Books
  {
     char  title[50];
     char  author[50];
     char  subject[100];
     int   book_id;
  };

  /* 函数声明 */
  void printBook( struct Books book );
  int main( )
  {
     struct Books Book1;        /* 声明 Book1，类型为 Book */
     struct Books Book2;        /* 声明 Book2，类型为 Book */
   
     /* Book1 详述 */
     strcpy( Book1.title, "C Programming");
     strcpy( Book1.author, "Nuha Ali"); 
     strcpy( Book1.subject, "C Programming Tutorial");
     Book1.book_id = 6495407;

     /* Book2 详述 */
     strcpy( Book2.title, "Telecom Billing");
     strcpy( Book2.author, "Zara Ali");
     strcpy( Book2.subject, "Telecom Billing Tutorial");
     Book2.book_id = 6495700;
   
     /* 输出 Book1 信息 */
     printBook( Book1 );

     /* 输出 Book2 信息 */
     printBook( Book2 );

     return 0;
  }
  void printBook( struct Books book )
  {
     printf( "Book title : %s\n", book.title);
     printf( "Book author : %s\n", book.author);
     printf( "Book subject : %s\n", book.subject);
     printf( "Book book_id : %d\n", book.book_id);
  }
  ```

- 指向结构的指针

  可以定义结构体的指针，类似变量和指针，

  ```c
  //声明指针
  struct Books *struct_pointer;
  //将变量的地址，赋值给指针
  struct_pointer = &Book1;
  //要想让这个指针能够访问到结构体中的某个成员，就需要用->运算符
  struct_pointer->title;//指针访问结构体中的title变量的值
  ```

  **上例使用指针模式：**

  ```C
  //其他不变
  ...
  printBook(&Book1);//这里传入的是指针
  ...
  void printBook(struct Books *book){//结构体的指针，作为参数，传递给函数
    printf("Book title:%s \n",book->title);//使用->运算符访问结构体成员
    ...
  }
  ```

- 位域

  又称"位段"，为了节省空间，仅存储占用一个或者几个二进制位的数据字节，满足一些特殊需要，如开关变量0和1,只用一个二进制位即可满足。

  所谓位域，就是吧一个字节中的二进制位，划分为几个不同的区域，位域有域名，可以在程序中用域名来操作，从而可以把不同的对象用一个字节的二进制位域来表示。

  >位域定义和变量说明
  >
  >```c
  >struct 位域结构名{
  >  位域列表
  >};
  >```
  >
  >其中位域列表的形式：
  >
  >`类型说明符 位域名:位域长度`

  示例：

  ```C
  struct bs{
    int a:8;
    int b:2;
    int c:6;
  };
  ```

  位域变量的说明与结构变量的说明方式相同，可采用先定义后说明，同时定义说明或者直接说明三种方式。

  ```C
  struct bs{
    int a:8;//占用的二进制位数8个
    int b:2;
    int c:6;
  } data;
  ```

  如上则是同时定义和说明一个`bs`的变量`data`，占用两个字节(两个8位)。

  ```C
  struct packed_stuct{
    unsigned int f1:1;
    unsigned int f2:1;
    unsigned int f3:1;
    unsigned int f4:1;
    unsigned int type:4;
    unsigned int my_int:9;//注意此处，前面有int，和下面的注意事项中不能超过一个字节的要求，并不矛盾，
  } pack;
  ```

  这里pack_struct就包含了6个成员，四个1位的标识符，一个4位的type，还有一个9位的my_int。

- 位于定义的注意事项

  - 一个位域必须存储在同一个字节中，不能跨两个字节，但是可以指定位域的开始位置：

    ```C
    struct bs{
      unsigned a:4;
      unsigned  :4;//空域
      unsigned b:4;//从下一单元开始存放，而不是使用上面空的那4个
      unsigned c:4;
    }
    ```

  - 位域不恩你个跨两个字节，所以位域长度不能超过8,超过了的话，可能会被重叠，或者放入下一个字节了。

  - 位域可以是无名位域，之用来占位，其不能使用。

    ```c
    struct k{
      int a:1;
      int  :2;//占位的，无名位域，不可用
      int b:3;
      int c:2;
    }
    ```

  其实，位域也算是一种结构类型，只不过成员是按照二进制位表示而已。

- 位域的使用

  类似与结构成员的使用

  ```C
  位域变量名.位域名
  ```

  位域允许各种格式输出，示例：

  ```C
  void main(){
    //定义了一个位域，并声明了一个变量bit，和它的指针变量*pbit
    struct bs{
      unsigned a:1;
      unsigned b:3;
      unsigned c:4;
    }bit,*pbit;
    //给位域赋值，注意不要超过位域的值的范围。
    bit.a = 1;//就只能是0或1,因为定义占1个二进制位
    bit.b = 7;//0--7,因为占用3个二进制位
    bit.c = 15;
    //以整型量输出
    printf("%d,%d,%d \n",bit.a,bit.b,bit.c);
    //使用位域指针访问成员
    pbit = &bit;//给指针赋值
    pbit->a = 0;//指针访问成员，并赋值给成员
    pbit->b&=3;//使用了&=运算符，相当与pbit->b = pbit->b&3;
    pbit->c|=1;//使用了|=运算符，相当与pbit-> = pbit->b|3;
    printf("%d,%d,%d \n",pbit->a,pbit->b,pbit-c);
  }
  ```

  