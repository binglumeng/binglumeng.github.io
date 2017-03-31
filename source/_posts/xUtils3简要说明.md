---
title: xUtils开源框架简要使用说明
date: 2017-01-21 15:38
tags: 
    - Android
    -xUtils
categories:
    - Android
---


> 前言：开源技术对于当前便捷开发的重要性，自不必言明。不重复造轮子，去学习和使用已有的开源框架和类库，有利于我们的产品快速开发与迭代实现。

xUtils3是基于xUtils的一个开源框架升级版，不再兼容Android4.0以下版本，虽然不如2016年的RxJava、retrofit等框架那么热门，但是也算是一个小而综合的开源框架，适合初学者学习使用。

## 1、xUtils3简介

xUtils是github上`wyouflf`作者的一个开源项目，继承了很多Android app开发常用的功能模块框架，主要有四大模块：DbUtils、ViewUtils、HttpUtils、BitmapUtils模块。包含了ORM框架、IOC框架，是的开发更为快捷。更为详细的介绍请异步github上的项目主页。[xUtils](https://github.com/wyouflf/xUtils),[xUtils3](https://github.com/wyouflf/xUtils3)以及在开源中国上发现的有简要的注释代码的[xUtils3](http://git.oschina.net/guozhiwei/xUtils3/tree/master)和[api文档](http://xutilsapi.oschina.mopaas.com/).感谢这些热心同学，感谢伟大的开源共享精神！

## 2、xUtils3使用

目前Android项目开发主流都在使用AndroidStudio为开发工具了，还在使用eclipse的同学，真心需要改变了，优势太多，无法一一赘述，简言之就是便捷、高效。Gralde中配置依赖

```groovy
compile 'org.xutils:xutils:3.3.40'
```

建议使用最新版本的依赖库。详情请看xUtils的github地址[xUtils3](https://github.com/wyouflf/xUtils3)，Eclipse的使用，可以下载对应提供的aar文件，去除jar包和so文件到`lib`文件中使用。

### 项目配置

- 权限，xUtils3中使用数据库存储和网络请求，所以需要以下权限

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

- 初始化，在Application的onCreate中初始化

```java
//自定义MyApplication 继承 Application
@Override
public void onCreate(){
  super.onCreate();
  x.Ext.init(this);
  x.Ext.setDebug(BuildConfig.DEBUG);//是否输出debug日志，开启日志输出，会影响一部分性能
  ...
}
```

### ViewUtils注解使用

- 注解来绑定UI和事件
- 免除`findViewById`和`setOnClickListener`等

```java
@ContentView(R.layout.activit_main)
public MainActivity extends Activity {
  @ViewInject(R.id.text)
  private TextView tvTest;
  ...
  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      x.view().inject(this);//注入View的初始化，如此，方能在Activity的java文件中使用@ContentView、@ViewInject等注解
      //需要说明的是，x.view().inject()有几个方法重载呢，可以注入fragment，也可在adapter中注入，或者其他自定义类中，需要使用x.view().inject(this,view);或者x.view().inject(holder,view);
    ...
  }
  //此处需要说明的是，@Event默认的即使View的onClick事件，所以其最简实现形式如下，省略了许多参数
  @Event(R.id.btnTest)
  private void toClick(View view){
    //点击事件响应处理
    ...
  }
  //而下面为完整实现形式，value可以是多个控件的数组，type表示该组控件的事件响应方式，以及设置注册这类事件的setter方式，事件响应的函数的方法名，对应的自定义的这个方法，名称无所谓，但是需要private以及方法签名中的参数，要保持和所使用控件原有的方法参数的一致性。
  
  /**
 * 1. 方法必须私有限定,
 * 2. 方法参数形式必须和type对应的Listener接口一致.
 * 3. 注解参数value支持数组: value={id1, id2, id3}
 * 4. 其它参数说明见{@link org.xutils.event.annotation.Event}类的说明.
 **/
  @Event(value = {R.id.btnSend、R.id.btnReceive},type = View.OnClickListener.class,setter="setOnClickListener",method="onClickListener")
  private void doClick(View view){//此处方法参数，(View view)是因为onClickListerner的方法参数就是（View view），若是其他方法，则换成对应的参数列表。
    ...
  }
  ...
}
```

### HttpUtils访问网络

- 便捷的下载、上传文件或提交数据，如下简单的使用方式，更多的可以参照项目`sample`和githbu上的说明。

```java
@Event(value = R.id.btn_test_baidu2)
private void onTestBaidu2Click(View view) {
    RequestParams params = new RequestParams("https://www.baidu.com/s");
    params.setSslSocketFactory(...); // 设置ssl
    params.addQueryStringParameter("wd", "xUtils");
    //或者其他参数设置，比如超时，读取时长，请记住这里需要网络权限，若是忘了添加，可能就报错，但是提示不明显，会让你蒙头。返回的result结果，需要在这里面处理，尽量不要拿出外面线程处理，因为是网络异步，可能会出现到外面数据一时间为空。
    x.http().get(params, new Callback.CommonCallback<String>() {
        @Override
        public void onSuccess(String result) {
            Toast.makeText(x.app(), result, Toast.LENGTH_LONG).show();
        }

        @Override
        public void onError(Throwable ex, boolean isOnCallback) {
            Toast.makeText(x.app(), ex.getMessage(), Toast.LENGTH_LONG).show();
        }

        @Override
        public void onCancelled(CancelledException cex) {
            Toast.makeText(x.app(), "cancelled", Toast.LENGTH_LONG).show();
        }

        @Override
        public void onFinished() {

        }
    });
}
```

### DbUtils数据库的使用

- 使用了orm框架更为简便的代码实现数据CURD
- 各种检索方式，支持事务等

```java
private String dbPath = Environment.getExternalStorageDirectory() + "/database";
    private final String DB_NAME = "test.db";
    //数据库业务层配置信息的初始化
    private DbManager.DaoConfig daoConfig = new DbManager.DaoConfig()
            .setDbName(DB_NAME)//数据库名称
            // 不设置dbDir时, 默认存储在app的私有目录.
            .setDbDir(new File(dbPath)) // "sdcard"的写法并非最佳实践, 这里为了简单, 先这样写了.
            .setDbVersion(1)//版本
            .setDbOpenListener(new DbManager.DbOpenListener() {
                @Override
                public void onDbOpened(DbManager db) {
                    // 开启WAL, 对写入加速提升巨大
                    db.getDatabase().enableWriteAheadLogging();
                }
            })
            .setDbUpgradeListener(new DbManager.DbUpgradeListener() {
                @Override
                public void onUpgrade(DbManager db, int oldVersion, int newVersion) {
                    // db.addColumn(...);
                    // db.dropTable(...);
                    // ...
                    // or
                    // db.dropDb();
                }
            });

    private final DbManager db = x.getDb(daoConfig);//DB manager的对象

    /**
     * 新增数据对象到数据库
     *
     * @param object
     * @return
     */
    public boolean add(Object object) {
        try {
            db.saveOrUpdate(object);
        } catch (DbException e) {
            e.printStackTrace();
            return false;
        }
        return true;
    }

    /**
     * 绑定id形式的新增数据
     *
     * @param object
     * @return
     */
    public boolean saveBindId(Object object) {
        try {
            return db.saveBindingId(object);
        } catch (DbException e) {
            e.printStackTrace();
            return false;
        }
    }
   /**
     * 删除学生信息
     *
     * @param id 学生的学号
     * @return 返回删除结果，true为成功，false为失败
     */
    public boolean deleteChild(int id) {
        try {
            db.deleteById(Child.class, id);
        } catch (DbException e) {
            e.printStackTrace();
            return false;
        }
        return true;
    }
    /**
     * 更新
     *
     * @param object 数据对象
     * @return
     */
    public boolean update(Object object) {
        try {
            db.saveOrUpdate(object);
            return true;
        } catch (DbException e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 查询所有学生信息
     *
     * @return
     */
    public List<Child> searchChild() {
        try {
            return db.findAll(Child.class);
        } catch (DbException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 根据学号查询学生信息
     *
     * @param id 学生学号
     * @return
     */
    public Child searchChildById(int id) {
        try {
            return db.findById(Child.class, id);
        } catch (DbException e) {
            e.printStackTrace();
        }
        return null;
    }


    /**
     * 删除Child表
     *
     * @return
     */
    public boolean dropChildTable() {
        try {
            db.dropTable(Child.class);
        } catch (DbException e) {
            e.printStackTrace();
            return false;
        }
        return true;
    }
```

`Child.java`注意，此文件删除了一写get、set方法和属性，只作为说明演示，特别需要注意的就是**构造函数**那块，个人经验总结

```java
//创建child表的方式，使用@Table注解，只有一个参数就是name
@Table(name = "child")
public class Child {
  //创建表格中的列，使用@Column注解，包含name，isId，autoGen，property等属性，也就是sql语句中的属性字段等。
    @Column(name = "cNum", isId = true, autoGen = false)
    private int cNum;//学号
    @Column(name = "cName")
    private String cName;//学生姓名
   
    @Column(name = "ic_num", property = "unique")
    private String icNum;//幼儿卡号码,唯一

    /**
     * 无参数构造法不可私有化，否则数据库表格创建异常
     * 且不设置自增，若使用无参构造，容易引起数据插入只有一条。
     */
    public Child() {

    }

    /**
     * 多参数构造函数，创建Child类的对象
     *
     * @param cNum  学号
     * @param name  姓名
     * @param sex   性别
     * @param grade 班级
     * @param age   年龄
     */
    public Child(int cNum, String name, String sex, String grade, int age) {
        this.cNum = cNum;
        this.cName = name;
        this.sex = sex;
        this.grade = grade;
        this.age = age;
    }

    /**
     * 获取幼儿学号
     *
     * @return
     */
    public int getCNum() {
        return cNum;
    }

    /**
     * 设置幼儿学号
     *
     * @param cNum
     */
    public void setNum(int cNum) {
        this.cNum = cNum;
    }

  
}
```

### BitmapUtils图片使用

- 使用`lru`算法，缓存以及多线程等，便于更好的管理图片下载，和加载操作。

```java
x.image().bind(imageView, url, imageOptions);

// assets file
x.image().bind(imageView, "assets://test.gif", imageOptions);

// local file
x.image().bind(imageView, new File("/sdcard/test.gif").toURI().toString(), imageOptions);
x.image().bind(imageView, "/sdcard/test.gif", imageOptions);
x.image().bind(imageView, "file:///sdcard/test.gif", imageOptions);
x.image().bind(imageView, "file:/sdcard/test.gif", imageOptions);

x.image().bind(imageView, url, imageOptions, new Callback.CommonCallback<Drawable>() {...});
x.image().loadDrawable(url, imageOptions, new Callback.CommonCallback<Drawable>() {...});
x.image().loadFile(url, imageOptions, new Callback.CommonCallback<File>() {...});
```

### LogUtils输出日志

```java
// 自动添加TAG，格式： className.methodName(L:lineNumber)
// 可设置全局的LogUtils.allowD = false，LogUtils.allowI = false...，控制是否输出log。
// 自定义log输出LogUtils.customLogger = new xxxLogger();
LogUtils.d("wyouflf");
```

### 混淆时注意事项：

- 添加Android默认混淆配置${sdk.dir}/tools/proguard/proguard-android.txt
- 不要混淆xUtils中的注解类型，添加混淆配置：-keep class * extends java.lang.annotation.Annotation { *; }
- 对使用DbUtils模块持久化的实体类不要混淆，或者注解所有表和列名称@Table(name="xxx")，@Id(column="xxx")，@Column(column="xxx"),@Foreign(column="xxx",foreign="xxx");



附言：本人算是IT技术小白，使用xUtils尚且不熟悉，且与此做个笔记记录，希望于人于己有所帮助。