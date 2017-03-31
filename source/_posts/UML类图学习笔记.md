---
title: UML类图笔记
date: 2016-12-30 16:08
tags: 
    - UML
categories:
    - 编程相关
---

# UML類圖簡要語法

UML圖形中，使用最多的應該是UML類圖了，瞭解類圖的使用與結構。**類：**封裝了數據和行爲，具有相同屬性、操作、關係的對象的集合的總稱。

系統分析與設計階段，類分爲：實體類、控制類、邊界類。

- 實體類：對應的是系統需求中的實體對象
- 控制類：對應系統的執行邏輯和業務操作
- 邊界類：對應系統的一些對外接口界面等

## 1、類圖

在UML中，類使用類名、屬性和操作放置與綫框内表示。`Employee`類

![employee](http://img.blog.csdn.net/20161230154833148?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```java
public class Employee{
  private String name;
  private int age;
  private String email;
  
  public void modifyInfo(){
    ...
  }
}
```

**説明：**類圖中由三部分組成：

- 類名：字符串形式的類名
- 屬性：類的成員變量`權限  名稱：類型 [ = 默認值]`
- 方法/函數：類的任意對象的行爲`權限  名稱(參數列表) [ : 返回類型]`

其中權限有三種：public、private、protected，對應的符號是`+`、`-`、`#`

![demo](http://img.blog.csdn.net/20161230154917227?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

Java語言中有内部類，也就出現了第四部分

![container](http://img.blog.csdn.net/20161230154946508?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 2、類圖之間關係

軟件系統中，類多不是孤立存在的，而存在多重關係

- 關聯

  `實綫鏈接`表示關聯關係，Java中可以理解為，一個類中包含了另外一個類的對象，則兩者為關聯關係。

  ![contains](http://img.blog.csdn.net/20161230155034685?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  ```java
  public class LoginForm{
    private JButton loginButton;//定為成員變量
    ...
  }

  public class JButton{
    ...
  }
  ```

  - 雙向關聯

    默認情況下，關聯是雙向的，顧客與商品，學生與老師。

    ![shuangxiang](http://img.blog.csdn.net/20161230155104154?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

    ```java
    public class Customer {
      private Product[] products;
      ...
    }
    public class Product {
      private Customer customer;
      ...
    }
    ```

  - 單向關聯

    單向關聯則用帶箭頭的實綫表示

  - 自關聯

    包含自身的關聯模式

    ![self](http://img.blog.csdn.net/20161230155221056?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  - 多重關聯

    Multiplicity複雜的關聯關係，用數字對應方式表示

    | 表示方式 | 多重性説明                     |
    | ---- | ------------------------- |
    | 1..1 | 另一類的一個對象，衹與該類的一個對象        |
    | 0..* | 另一類的一個對象，與該類的0個或多個對象有關係   |
    | 1..* | 另一類的一個對象，與該類的一個或多個對象有關係   |
    | 0..1 | 另一類的一個對象，沒有或衹與該類的一個對象有關係  |
    | m..n | 另一類的一個對象，與該類至少m，最多n個對象有關係 |

    示例，一個界面可有多個Button，一個Button衹能屬於一個或者不屬于任何界面。

    ![button](http://img.blog.csdn.net/20161230155342673?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

    ```java
    public class From{
      private Button[] buttons;//button的集合
      ...
    }
    public class Button{
      ...
    }
    ```

  - 聚合關係

    整體與部分的關係，成員可以不必以來整體存在，使用菱形箭頭實綫

    ![car](http://img.blog.csdn.net/20161230155624683?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

    ```java
    public class Car{
      private Engine engine;
      //構造函數
      public Car(Engine engine){
        this.engine = engine;
      }
      //set
      public void setEngine(Engine engine){
        this.engine = engine;
      }
      ...
    }

    public class Engine{
      ...
    }
    ```

  - 組合關係

    表示整體與部分，但是部分不能脫離整體存在，用實心菱形箭頭實綫表示

    ![human](http://img.blog.csdn.net/20161230160009245?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

    ```java
    public class Head{
      private Mouth mouth;
      public Head(){
        mouth = new mouth();//實例化成員類
      }
      ...
    }
    public class Mouth {
      ...
    }
    ```

- 依賴關係

  使用虛綫表示依賴

  ![driver](http://img.blog.csdn.net/20161230160029354?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  ```java
  public class Driver {
    public void drive(Car car){
      car.move();
    }
    ...
  }
  public class Car{
    public void move(){
      ...
    }
    ...
  }
  ```

- 汎化關係

  汎化關係也就是繼承關係，使用實綫空心三角箭頭表示

  ![person](http://img.blog.csdn.net/20161230160114202?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  ```java
  //父类
  public class Person {
  protected String name;
  protected int age;

  public void move() {
          ……
  }

      public void say() {
      ……
      }
  }

  //子类
  public class Student extends Person {
  private String studentNo;

  public void study() {
      ……
      }
  }

  //子类
  public class Teacher extends Person {
  private String teacherNo;

  public void teach() {
      ……
      }
  }
  ```

- 接口實現

  接口是沒有屬性的抽象方法的集合，實現使用虛綫空心三角箭頭表示，接口左上角有標志。

  ![interface](http://img.blog.csdn.net/20161230160146187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ2x1bWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  ```java
  public interface Vehicle {
  public void move();
  }

  public class Ship implements Vehicle {
  public void move() {
      ……
      }
  }

  public class Car implements Vehicle {
  public void move() {
      ……
      }
  }
  ```
