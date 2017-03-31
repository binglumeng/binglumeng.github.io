---
title: C语言学习笔记--基础语法三
date: 2016-12-18 19:50
tags: 
    - C
categories:
    - 编程相关
---
# C語言學習筆記--基礎語法三

## 1、共用體與位域

​	**共用體**，是一種特殊的數據類型，允許在相同的內存位置，存儲不同的數據類型。可以定義一個帶有多個成員的共用體，但是使用的時候，只能有一個成員有效的擁有數值。

​	共用體提供了一種使用相同內存的有效方式。

- 定義共用體

  `union`關鍵字，類似與結構體的定義。

  ```C
  union [union tag]{
    member definition;
    member definition;
    ...
    member definition;
  }[one or more union variables];
  ```

  union tag爲可選項，類似結構體的定義，可以先聲明，也可以聲明並定義。

  ```C
  #include <stdio.h>
  #inclued <string.h>
  //這個共用體，聲明了一個Data共用體，它的變量data，以及包含了三種數據類型，i、f、str[20]，共用一個內存地址，這塊內存大小由共用體內最大成員決定。此處就是char類型的str，有20個字節位置。
  union Data {
    int i;
    float f;
    char str[20];
  };
  //main()函數
  void main(){
    //聲明共用體變量。
    union Data data;
    //顯示共用體內存佔用大小
    printf("data的內存佔用：%d \n",sizeof(data));
  }
  //編譯運行，結果會顯示爲20
  ```

  共用體成員的訪問也類似與結構體，使用`.`符號。

  ```C
  #include <stdio.h>
  #include <string.h>
  union Data {
    int i;
    float f;
    char str[20];
  };
  void main(){
    union Data data;
    //賦值
    data.i = 10;
    data.f = 220.5;
    strcpy(data.str,"C Programming");
    //輸出
    printf("data.i: %d \n",data.i);
    printf("data.f: %f \n",data.f);
    printf("data.str: %s \n",data.str);
    //這樣的輸出結果，只有最後一次會輸出正確的結果，上兩次都會數據損壞，因爲共用體只有一個可用內存變量。
    //如果每次賦值後都輸出一下，是沒問題的，或者同時使用同一個成員變量，可以更改變量值，也是可行的。
    data.i = 20;
    printf("data.i: %d \n",data.i);
    data.i = 99;
    printf("data.i %d \n",data.i);
  }
  ```

- 位域

  位域雖然上一節也有講，但是不清楚，這麼理解吧，位域、結構體、共用體三個概念比較理解，結構體和位域用`struct`,而共用體用`union`關鍵字

  | 名稱   | 是否共用內存                              | 備注                                       |
  | ---- | ----------------------------------- | ---------------------------------------- |
  | 結構體  | 內部成員各自有自己聲明的內存空間                    | 看作JAVA語言中的一個bean實體類                      |
  | 共用體  | 內部成員共享內存地址和空間，最大的決定整體空間大小           | 類似與槍支，可能適配多個型號的子彈，但是每一次開槍，槍管裏就只能一個類型的子彈（姑且這麼比喻吧） |
  | 位域   | 類似共用體，但是算作特殊的結構體，可以單個指定成員佔用的內存二進制位數 | 算是一個只有一種類型成員的共用體，暫且這麼理解。                 |

  示例：
```C
  #include <stdio.h>
  #include <sring.h>
  //定義簡單的結構體
  struct{
    unsigned int widthValidated;
    unsigned int heightValidated;
  } status1;
  //定義位域
  struct {
    unsigned int widthValidated : 1;
    unsigned int heightValidated :1;
  } status2;
  //main()
  void main(){
    printf("status1佔用內存：%d \n",sizeof(status1));
    printf("status2佔用內存：%d \n",sizeof(status2));
  }
  //結果會分別是8位和4位，因爲結構體算是兩個成員獨立佔用，而位域，算是兩個成員共用，而且雖然佔用4個字節，但是數據只用了1位，若是int widthValidated : 5; 那麼就會是8位了吧，位域不恩那個超出的，也不太清楚了，以後再回來看
```

  位域的聲明
```c
  struct {
    /*type爲整數類型，決定了如何解釋位域的值,
    member_name爲位域的名稱，
    width爲位域中位的數量，不能大於制定類型的位寬度，不如int，就不能大於4個字節。
    */
    type [member_name] : width;
  }
```

  位域指定了變量的位數，也就限制了數值的範圍，超出後會無法正確完成

```C
  #include <stdio.h>
  #include <string.h>
  //define a bitrange
  struct {
    //限定範圍了，0--7
    unsigned int age:3;
  } Age;
  void main(){
    Age.age = 4;
    printf("Sizeof(Age):%d \n",sizeof(Age));
    pringf("Age.age : %d \n",Age.age);
    //但是不能超過3位二進制數的大小，
    Age.age = 8;
    printf("Age.age : %d \n",Age.age);
    //這時候編譯會警告，數值就會是默認的0
  }
```

- typedef

  `typedef`C語言提供的一個用於給類型起新名字的關鍵字。類似與linux系統`alias`命令，就是用於自定義別名的一個關鍵字指令。

  ```C
  //定義unsigned char 爲BYTE
  typedef unsigned char BYTE;
  //那麼就可以用來聲明變量
  BYTE b1;//就相當與unsigned char b1;
  ```

  習慣性的將`typedef`別名化的類型寫作大寫，當然也可以小寫。可以作用與基本數據類型，也可以作用與結構體、共用體、位域之類的自定義類型。

- typedef 和 #define

  `#define`是C指令，用於定義數據類型的別名，類似`typedef`

  - typedef僅用於爲類型定義別名，而#define也可以定義數值的別名，如1可以定義爲ONE
  - typedef由編譯器執行解釋，#define由預編譯器處理

  ```C
  #include <stdio.h>

  //定義
  #define TRUE 1
  #define TWO 22
  void main(){
    printf("TURE:%d,TWO:%d \n",TURE,TWO);
  }
  ```

- I/O

  C語言提供了一系列的內置函數用於輸入和輸出操作。

  > - 標準文件
  >
  >   C語言把左右設備都當作文件處理，類似與linux下，一切皆文件。
  >
  >   | 標準文件 | 文件指針   | 設備   |
  >   | ---- | ------ | ---- |
  >   | 標準輸入 | stdin  | 鍵盤   |
  >   | 標準輸出 | stdout | 屏幕   |
  >   | 標準錯誤 | stderr | 您的屏幕 |
  >
  >   文件指針是訪問文件的方式。
  >
  > - getchar()和putchar()函數
  >
  >   `int getchar(void)`函數從屏幕讀取下一個可用字符，一次讀一個，可以房子循環裏面使用。
  >
  >   `int putchar(Int c)`函數向屏幕輸出字符，一次輸一個，循環使用。
  >
  >   ```C
  >   #include <stido.h>
  >   void main(){
  >     int c;
  >     printf("請輸入字符：");
  >     c = getchar();
  >     printf("\n 您輸入的字符是：");
  >     putchar(c);
  >   }
  >   ```
  >
  > - gets()和puts()
  >
  >   **char \*gets(char \*s)**函數從`stdin`讀取一行到`s`所指向的緩衝區，一知道遇到終止符或者`EOF`。
  >
  >   **int puts(const char \*s)**函數把字符串s和一個尾隨的換行符寫入到`stdout`。
  >
  >   ```c
  >   #include <stdio.h>
  >   void main(){
  >     char str[100];//緩存區域
  >     printf("Enter a value：");
  >     gets(str);
  >     printf("\n Your value:");
  >     puts(str);
  >   }
  >   ```
  >
  > - scanf()和printf()
  >
  >   `int scanf(const char \*format,...)`函數從標準輸入流`stdin`讀取輸入，根據`fromat`來瀏覽輸入。
  >
  >   `int printf(const char \*format,...)`函數吧輸出以`format`格式顯示，可以是`%s、%d、%c、%f`等。
  >
  > **Note:**`scanf()`讀取輸入，需要輸入的數據格式跟`format`的一樣才行，不然會報錯。遇到空格會停止讀取，便認爲結束了一個字符的讀取。

- 文件讀寫

  - 打開文件，`fopen()`函數來創建一個新的，或者打開已有的文件。

  ```C
  FILE *fopen(const char * filename,const char * mode);
  ```

  其中`finename`是字符串，命名文件用的。`mode`是讀寫模式

  | mode | description                          |
  | ---- | ------------------------------------ |
  | r    | 打開一個已有的文本文件，允許讀取文件                   |
  | w    | 打開一個文本文件，允許寫入文件。若不存在，會新建文件，從頭開始寫起    |
  | a    | 打開一個文本文件，追加模式寫入，不存在則新建               |
  | r+   | 打開一個文本文件，允許讀寫文件                      |
  | w+   | 打開一個文本文件，允許讀寫。若已存在，文件會被截斷爲另長度，不存在則新建 |
  | a+   | 打開一個文本文件，允許讀寫。不存在則新建，讀取從頭開始，寫入則是追加模式 |

  若是處理的是二進制文件，寫法有點差異,多個`b`字符
```C
  "rb","wb","ab","rb+","r+b","wb+","w+b","ab+","a+b"
```

  - 關閉文件，`fclose()`關閉文件的函數

  `int fclose(FILE *fp);`若關閉成功，返回`0`，關閉失敗，返回`EOF`（定義在`stdio.h`中的常量）

  - 寫入文件

    `fputc()`函數，將c的字符，寫入到fp的輸出流，成功則返回寫入的字符，失敗返回EOF。

    ```c
    int fputc(int c,FILE *fp);
    ```

    `fputc()`函數也可以吧字符串s寫入到fp的輸出流中，成功返回非負值，失敗返回EOF。

    ```c
    int fputs(const char *s,FILE *fp);
    int fpuintf(FILE *fp,const char *format,...);
    ```

    示例，會在當前文件夾下，生成text文檔，含有兩句話。

    ```C
    #include <stdio.h>
    void main(){
      FILE *fp;
      fp = fopen("./text.txt","w+");
      fprintf(fp,"This is testing for fprintf...\n");
      fputs("這是測試fputs輸入\n",fp);
      fclose(fp);
    }
    ```

  - 讀取文件

    `fgetc`和`fgets`函數

    ```c
    //讀取一個字符，正確返回該字符，錯誤返回EOF
    int fgetc(FILE * fp);
    //讀取字符串流，直到讀取到null的標識符，所以之前讀取了有效的n-1個,要是遇到\n或者EOF則會返回讀取的字符。
    char *fgets(char *buf,int n,FILE *fp);
    //從文件中讀取，遇到空格會停止
    int fscanf(FILE *fp,const char *format,...);
    ```

    示例，讀取上面的文件

    ```C
    #include <stdio.h>
    void main(){
      FILE *fp;
      char buff[255];
      fp = fopen("./text.txt","r");
      //只會讀取第一個單詞，因爲遇到空格了
      fscanf(fp,"%s",buff);
      printf("1: %s\n",buff);
      //會讀取一句話，遇到\n或者EOF結束
      fgets(buff,255,(FILE*)fp);
      printf("2: %s\n",buff);
      
      fgets(buff,255,(FILE*)fp);
      printf("3: %s\n",buff);
      fclose(fp);
    }
    ```

    首先`fscanf`只讀取了`This`，因爲它遇到了空格，之後調用`fgets`讀取剩餘部分，知道遇到`\n`或者EOF，而第二次調用`fgets`讀取了一整句。

  - 二進制讀寫函數

    ```C
    size_t fread(void *ptr,size_t size_of_elements,size_t number_of_elements,FILE *a_file);
    size_t fwrite(const void *ptr,size_t size_of_elements,size_t size_of_elements,FILE *a_file);
    ```

    常用與存儲塊的讀寫，通常**數組**或**結構體**

## 2、預處理器

預處理器不是編譯器的組成部分，其會在編譯器實際編譯之前，指示編譯器做一些預處理工作。C與處理器(C Preprocessor)簡稱`CPP`，以`#`開頭，位於行首，文件首。

| 指令        | 描述                 |
| --------- | ------------------ |
| \#define  | 定義宏                |
| \#include | 包含一個源代碼文件          |
| \#undef   | 取消已定義的宏            |
| \#ifdef   | 如果定義了宏，返回真         |
| \#ifndef  | 如果沒有定義宏，返回真        |
| \#if      | 條件語句，滿足條件，則執行下面代碼  |
| \#else    | 與if搭配使用            |
| \#elif    | 也就是else if語句快      |
| \#endif   | 結束一個#if...#else語句塊 |
| \#error   | 遇到標準錯誤時，輸出錯誤       |
| \#pragma  | 使用標準化方法，向編譯器發布特殊指令 |

示例：

```C
//定義常量別名,預處理指令，不需要;分行寫
#define MAX 10
//引入頭文件
#include <stdio.h>
#include "myheader.h"
//取消宏定義，並更改
#undef FILE_SIZE
#define FILE_SIZE 88;
//條件判斷，預處理的，與代碼塊的if..else不用弄混哦
#ifndef MSG
	#define MSG "message"
#endif
```

- 預定義宏

  **ANSI C**定義了許多宏，但是不能直接修改

  | 宏        | 描述                     |
  | -------- | ---------------------- |
  | \_DATE\_ | 當前日期，”MMM DD YYYY"格式顯示 |
  | \_TIME\_ | 當前時間，"HH:MM:SS"格式顯示    |
  | \_FILE\_ | 這會包含當前文件名，一個字符串常量      |
  | \_LINE\_ | 這會包含當前行號，一個十進制常量       |
  | \_STDC\_ | 當編譯器以ANSI標準編譯時，則定義爲1   |

- 預處理器運算符

  - 宏延伸`\`

    一個宏通常寫在一行，若是太長，可以用`\`符號延伸

    ```C
    #define message_for(a,b) \
    	printf(#a"and"#b ":Hello\n");
    ```

  - 字符串常量化`#`

    定義宏時候，參數轉化爲字符常量，需要用`#`

    ```C
    #include <stdio.h>
    #define msg(a,b) \
    	printf(#a "和" #b "是朋友");//此處就是用了#符號，
    void main(){
      msg("小白","小黑");
    }
    ```

    輸出結果：

    ```C
    小白和小黑是朋友
    ```

  - 標記粘貼`##`

    合並兩個參數

    ```C
    #include <stdio.h>
    #define paster(n) printf("token：" #n " = %d",token##n)
    void main(){
      int tocken22 = 80;
      paster(22);//這裏輸入22,在paster函數內合並參數，就得到了token22,然後輸出token22這個變量，就得到了下面的結果，分寫一下printf函數，字符token+#n，也就是token22,token##n，代表了上面定義int token22
    }
    ```

    輸出結果：

    ```C
    token22 = 80
    ```

    可以判斷定義

    ```C
    #inclue <stdio.h>
    #if !defined(MSG)
    	#define MSG "hahaha"
    #endif
    void main(){
      printf("msg == %s\n",MSG);
    }
    ```

  - 參數化的宏

    CPP可以使用參數化的宏來模擬函數

    ```C
    //求平方的函數
    int square(int x){
      return x * x;
    }
    //使用宏來定義,名稱和參數的括號之間不能有空格，且緊跟#define
    #define square(x) ((x)*(x))
    ```

    然後就可以在源文件中使用

    ```C
    #include <stdio.h>
    #define MAX(x,y) ((x)>(y)?(x):(y))
    void main(){
      printf("Max between 20 and 10 is : %d\n",MAX(20,10));
    }
    ```

- 頭文件

  `.h`文件，包含了C函數聲明和宏定義，被多個源文件共享。分爲**編譯器自帶**和**程序員自定義**兩類。用`#include`導入。

  ```C
  //<>包裹的，是系統的.h文件，""包裹的，是程序員自定義的.h文件
  #include <stdio.h>
  #include "myheader.h"
  //都可以通過編譯源碼時候，-l選項，將文件置於列表前。
  ```

  其實，頭文件，就相當與復制，類似與java中的導入包和文件類。導入一次就夠了，不用多次導入。一般需要判斷一下

  ```c
  #ifndef HEADER
  #define HEADER
  ...
  #endif
  ```

  有條件引用

  ```c
  #if SYSTEM_1
  	#include "system_1.h"
  #elif SYSTEM_2
  	#include "system_2.h"
  //太多的話，可以宏定義一下
  #define SYSTEM_H "system_1.h"
  ```

## 3、類型轉換

編程語言中常見的數據類型轉換，從低類型轉高類型，從高類型轉低類型。向下轉換不安全的，會丟失數據精度。

```C
(type_name) variable;
```

- 整數提升

  其實也就是一種類型轉換，將小範圍類型，轉爲大範圍的，char--int

  ```c
  char i = 'c';//ascii碼 99
  int a = 17;
  int sum = i + c;
  //得到的sum值，就是int的，116
  ```

- 常用轉換

  一般都會隱式的向上轉型，向下轉型會丟失精度，且需要強制轉型。

  ![translate](http://img.blog.csdn.net/20161218194455669?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  這並不適合`賦值運算符`、邏輯運算符的`&&`、`||`

- 錯誤處理

  C語言不提供對錯誤的處理，其返回值出錯時多是1或者NULL，同時生成錯誤碼errno，可以在`<error.h>`中找到各種錯誤碼。通常通過返回值查找錯誤。一般初始化設這errno=0;

  **errno、perror()和strerror()**，C語言提供了兩個函數來顯示errno的錯誤信息。

  - perror()函數顯示您傳送的字符串，後面一個冒號、一個空格、和當前errno的文本描述
  - strerror()函數返回一個指針，指針指向errno的錯誤描述。

  示例：

  ```C
  #include <stdio.h>
  #include <errno.h>
  #include <string.h>

  extern int errno;
  int main(){
    FILE * pf;
    int errnum;
    //以二進制形式打開文件，此處文件不存在，用於模擬錯誤
    pf = fopen("./no.txt","rb");
    if(pf==NULL){
      errnum = errno;
      fprintf(stderr,"錯誤號： %d \n",errno);
      perror("通過perror函數輸出錯誤");
      fprintf(stderr,"strerror函數顯示，打開文件錯誤： %s \n",strerror(errnum));
    }else{
      fclose(pf);
    }
    return 0;
  }
  ```

  錯誤種類很多，非法參數，數組越界之類的。程序正常退出時候會帶有一個EXIT_SUCCESS值，其爲宏定義，爲0。錯誤時候EXIT_FAILURE，爲-1。