---
title: C语言学习笔记--基础语法四
date: 2016-12-19 11:18
tags: 
    - C
categories:
    - 编程相关
---

## 递归

递归是指在函数的定义中使用函数自身的方法，简言之就是自我调用。

```C
void recursion(){
  recursion();//函数自我调用
}
int main(){
  recursion();
}
```

**Note:**使用递归要注意需要有跳出循环的条件，比如阶乘、斐波那契数列等常用到递归。

- 示例

  `阶乘`

```C
#include <stdio.h>
double factorial(unsigned int i){
  if(i <= 1){
    return 1;
  }
  return i * factorial(i-1);
}
void main(){
  int i = 15;
  printf("%d 的阶乘为 %f \n"，i，factorial(i));
}
```

`Fibonaci斐波那契数列`

```C
#include <stdio.h>
int fibonaci(int i){
  if(i==0){
    return 0;
  }
  if(i ==1){
    return 1;
  }
  return fibonaci(i-1)+fibonaci(i-2);
}
void main(){
  int i;
  for(i = 0;i<10;i++){
    printf("%d\t\n",fibonaci(i));
  }
}
```

## 可变参数

多数编程语言中都会有可变参数的函数形式

```C
//可变参数，函数内...省略号表示不定个数的参数，在调用时，可以传入数目不定，但是类型总是一致的。
int func(int,...){
  .
  .
  .
}
void main(){
  //调用可变参数的函数，传入参数的个数可以是不同的
  func(1,2,3);
  func(1,2,3,4);
}
```

**注：**使用可变参数函数，需要导入`stdarg.h`头文件，其中定义了相关的**宏和函数**步骤：

- 定义一个可变参数函数，最后一个参数为`...`省略号，前面一个参数总是`int`表示参数的个数
- 函数中创建一个`va_list`类型变量，该类型定义在`stdarg.h`中
- 使用**int**参数和**va_start**宏来初始化**va_list**变量为一个参数列表
- 使用**va_arg**宏和**va_list**变量来访问参数列表中的每个项
- 使用**va_end**来清理赋予**va_list**变量的内存

示例：

```C
#include <stdio.h>
#include <stdarg.h>
//定义一个求平均数的函数，传入的参数不定个数
double average(int num,...){
  va_list valist;
  double sum = 0.0;
  int i;
  //为 num 个参数初始化 valist
  va_start(valist,num);
  //访问所有赋值给valist的参数
  for(i=0; i<num;i++){
    sum+=va_arg(valist,int);
  }
  //清理为 valist 保留的内存
  va_end(valist);
  return sum/num;
}
int main(){
  printf("Average of 2,3,4,5 = %f \n",average(4,2,3,4,5));
  printf("Average of 5,10,15 = %f \n",average(3,5,10,15));
}
```

## 内存管理

C语言为内存的分配和管理提供了几个函数`stdlib.h`文件中

```C
#include <stdio.h>
#include <stdlib.h>
//该函数分配一个带有 num 个元素的数组，每个元素大小 size
void *calloc(int num,int size);
//该函数释放 address 所指向的内存块
void fress(void *address);
//该函数分配一个 num 个字节的数组，并初始化
void *malloc(int num);
//该函数重新分配内存,把内存扩展到newsize
void *realloc(void *address,int newsize);
```

编程时，若预先知道数组大小，便容易定义`char name[100]`若不确定数组大小，则可以用**指针**来指向未定义所需内存大小的字符，后期动态分配内存。

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(){
  //已知大小的定义方式
  char name[100];
  //不确定大小，使用指针作
  char *description;
  
  strcpy(name,"Hello C");
  //动态分配内存
  description = malloc(200 * sizeof(char));
  //也可以用calloc()函数替代malloc()
  if(description == NULL){
    fprintf(stderr,"Error,不能分配所需内存\n");
  }else{
    strcpy(description,"Hello C ,非常不错的编程语言\n")；
  }
  printf("Name = %s \n",name);
  printf("Description:%s \n",description);
}
```

程序退出时，会自动释放内存，但是依然建议在不需要内存时候，手动调用`free()`函数释放内存。也可以通过`realloc()`调整内存大小

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
int main(){
  char name[100];
  char *description;
  strcpy(name,"Boby");
  //动态分配内存
  description = calloc(30,sizeof(char));
  if(description == NULL){
    fprintf(stderr,"错了，又没有分配好内存\n");
  }else {
    strcpy(description,"Boby,你好啊\n");
  }
  //重新调整内存大小
  description = realloc(description,100*sizeof(char));
  if(description == NULL){
    fprintf(stderr,"好吧，又错了,内存没有调整正确\n";)
  }else{
  	strcat(description,"Boby,你在哪儿啊\n");  
  }
  printf("Name = %s \n",name);
  printf("description : %s \n",description);
  //释放内存
  free(description);
}
```

## 命令行参数

执行程序时，可以从命令行传值给C程序，这类值称为**命令行参数**，当其需要从外部控制程序时候，这类参数就很重要了。

命令行参数，是使用`main()`函数参数来处理，其中`argc`是传入参数的个数，`argv[]`是一个指针数组，指向传递给程序的每个参数。

```C
#include <stdio.h>
int main(int argc,char *argv[]){
  if(argc == 2){
    printf("传递来的参数是%s\n"，argv[1]);
  }else if(argc > 2){
    printf("传递来了许多参数\n");
  }else{
    printf("只有一个参数\n");
  }
}
```

**Note:**需要说明的是`argv[0]`存储程序的名字，`argv[1]`才是第一个参数的指针，`*argv[n]`是最后一个参数，如果没有任何参数，argc就是1，传递一个参数，**argc**就是2;

> 多个命令行参数之间用空格分隔，多是参数本身带有空格，那就放在`""`或`''`之内

```C
$ gcc test.c
$ ./test.out argument1,argument2
$ ./test.out argument1,"argument with blank"
```