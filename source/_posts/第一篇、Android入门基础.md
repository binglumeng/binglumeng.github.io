---
title: "Android入门基础"
date: 2017-03-27 16:57
author: 冰路梦
tag:
    - Android
categories:
    - Android
---
## 第一章、Android入门基础

### 1.$建立App$

Intent 启动activity。传递参数。有显示和隐式的区分。explicit intent和implicit intent。

### 2.$ActionBar$

- 声明父类Activity，在AndroidManifest.xml文件中,Activity节点下，有activity:parentActivityName的属性，并在ActionBar中调用getSupportActionBar().setDisplayHomeAsUpEnabled(true);方法，如此点击ActionBar的返回键，会跳转到其声明的super Activity而不会返回到启动它的那个activity了。
- 在application节点或者activity节点下，配置theme属性，可以更改actionbar的主题，可以自定义主题style.xml文件，一般要继承parent一个theme，然后item各个属性，配置自定义的颜色或者图片样式。可以自定义selector美化样式。
- actionbar可以调用hide和show方法，达到隐藏显示的效果，通过android:windowActionBarOverlay设置为true的属性方法，来启动叠加。避免隐藏和显示actionbar而造成界面大小的调整重绘。`兼容低版本，有support库，好多属性和高版本属性一样名称，但是前面没有android:如android:paddingTop="?attr/actionBarSize"问号后就没有android:表明这是support库的属性。`

### 3.$Compatibile Devices$

- 语言：res下的value目录，建立不同的value，适配不同的语言。如value-en,value-zh
- 屏幕尺寸：res下layout配置不同，有四种尺寸和分辨率小(small)，普通(normal)，大(large)，超大(xlarge)；低精度(ldpi), 中精度(mdpi), 高精度(hdpi), 超高精度(xhdpi)；如layout-large,layout-land,layout-large-land等等。其中分辨率和屏幕密度有关，ldpi=0.75,mdpi=1,hdpi=1.5,xhdpi=2;

### 4.$ActivityLifeCircle$

- Activity生命周期金字塔![base_life_circle](第一篇、Android入门基础/basic-lifecycle.png "Activity生命周期图")

  > 如果在onCreated方法中调用了finish方法，则会直接调用onDestoryed方法，而不会走其他流程。
  >
  > 在onPaused方法中做一些资源释放比较好，数据保存之类的可以放在onStoped方法中。对应的在onResumed方法中恢复一些资源。**Activity的非正常销毁，并不一定会被调用onDestoryed方法**  
  >
  > Activity会自动保存一些view控件类的状态到bundle中，用于异常时候恢复。然而其他数据则需要手动保存。onSaveInstanceState()重写来保存数据(Activity需要重建才会调用它)，在activity重建时候，会将数据传递到bundle给oncreate或者onRestoreInstanceState()。  
  >
  > 跳转到其他activity或者按home键，都会调用onSaveInstanceState方法，然而从被跳转Activity返回上一个activity，其不会调用onSaveInstanceState。`在onCreated中需要判断bundle是否为空，而在onRestoreInstanceState中，不需要。`

### 5.Fragment

- Fragment 必须复写onCreateView方法，inflate布局文件或view。但是xml布局写fragment的方式不能动态删减fragment。所以可以在代码中添加fragment，但是需要在布局拥有一个layout容器。

   用fragment manager来管理，需要启用事务，transaction例如

```java
FragmentTransaction transaction= getSupportFragmentManager().beginTransaction();
transaction.replace(R.id.fragment_container, newFragment);//可以add,hide
transaction.addToBackStack(null);//设置用于用户回退操作。参数为事务名。
// Commit the transaction
transaction.commit();
```
- Fragments之间交互，要通过Activity，在Fragment中定义接口和方法，activity实现。然后fragment调用方法后，会传递到activity中。

### 6.保存数据

- context.getSharedPreferences()，需要设置名称，而activity.getPreferences()获得默认的。似乎还有个getDefaultPreferences();的方法。写用sp的editor，commit。读取用sp的get。
- 保存到文件，有内部外部之分，内部和外部的getExternalFilesDir()会与app共存亡，而getPublicFile。内部有getFile和cache。可以通过createCacheFile方法创建缓存文件。外部public文件夹，需要指明类别DIRECTORY_PICTURES等。使用前需要判断sd卡状态，和空间getFreeSpace(),getTotalSpace().删除文件可以context.deletefile().
- getWriteable和getReadable，都是可读写的对象，不过getReadable先尝试获取可写，不行，再只读。而writeable就报错了。

### 7.与其他应用交互

- #### Intent的发送

  > 1. intent发送出去，一般情况下Android会保证有应用接收intent，但是若真的没有应用接收intent，那么app会崩溃。此时可以检测是否有接收intent的应用，若没有，可提供下载链接或者终止操作。
  >
  >    ```java
  >    PackageManager packageManager = getPackageManager();
  >    List<ResolveInfo> activities = packageManager.queryIntentActivities(intent, 0);
  >    boolean isIntentSafe = activities.size() > 0;
  >    ```
  >
  > 2. 一般start Activity有多个响应的话，用户可以选择默认程序，下次就不会弹出选择。然而分享功能，就需要必须显示所有响应的app，可用如下代码：createChooser来创建intent，还能设置标题，并在无应用响应时候提示。
  >
  > ```java
  > Intent intent = new Intent(Intent.ACTION_SEND);
  > ...
  > // Always use string resources for UI text. This says something like "Share this photo with"
  > String title = getResources().getText(R.string.chooser_title);
  > // Create and start the chooser
  > Intent chooser = Intent.createChooser(intent, title);
  > startActivity(chooser);
  > ```

- #### 接收Activity返回的结果

  > requestCode，ResultCode,请求码和返回码，要知道返回数据类型结构，才能正确使用。

- #### Intent过滤

  > 1. Intent设置Action调用相应的app，对应的app需要在android manifest清单文件中，activity节点注册相应的action，intent-filter。Data属性可以更为具体细致的过滤action的数据请求。而category一般少用，都是默认。但是也必须声明.
  >
  >    ```xml
  >    <activity android:name="ShareActivity">
  >        <intent-filter>
  >            <action android:name="android.intent.action.SEND"/>
  >            <category android:name="android.intent.category.DEFAULT"/>
  >            <data android:mimeType="text/plain"/>
  >            <data android:mimeType="image/*"/>
  >        </intent-filter>
  >    </activity>
  >    ```
  >
  >    *若是不同的action拥有不同的data，则需要分开写两个intent-filter*
  >
  > 2. 一般onCreate或onStart中getIntent接收intent，做数据处理。其他地方可set Result来返回结果给调用该Activity的Activity。一般要指定result Code，若是没处理setResult，比如按了back则会有默认的ResultCancel返回。
  >
  > 3. setResult也不一定非得是返回码标志，若是请求结果就要int数值，则可以直接返回一个>0的int数值回去。本Activity不必在意是被start Activity还是forResult，系统会判断。
  >
  > ```java
  > setResult(RESULT_COLOR_RED);
  > finish();
  > ```
