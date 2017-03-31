---
title: "Android分享操作"
date: 2017-03-27 16:57
author: 冰路梦
tag:
    - Android
categories:
    - Android
---
## 第二章、Android分享操作

### 1. 分享简单数据

- #### 向其他app发送检单数据

  >1. Intent发送数据必须有actions，其他对应action的activity响应事件。通过Intent实现分享功能，而ActionBar可使用`ShareActionProvider`来快速实现分享功能。如下是使用Intent.createChooser实现分享功能的demo：
  >
  >```java
  >Intent sendIntent = new Intent();
  >sendIntent.setAction(Intent.ACTION_SEND);
  >sendIntent.putExtra(Intent.EXTRA_TEXT,"This is My Text to send.");
  >sendIntent.setType("text/plain");
  >startActivity(Intent.createChooser(sendIntent,"Share to My friends");
  >```
  >
  >*如上，可以显示出多个选择框，列出可响应的分享社交App*
  >
  >2. 分享二进制文件，需要指定特定的MIME类型，在EXTRA_STREAM里面放置数据的URI，如下分享一个图片的代码示例:
  >
  >   ```java
  >   Intent shareIntent = new Intent();
  >   shareIntent.setAction(Intent.ACTION_SEND);
  >   shareIntent.putExtra(Intent.EXTRA_STREAM, uriToImage);//重要，指定URI
  >   shareIntent.setType("image/jpeg");//指定MIME类型
  >   startActivity(Intent.createChooser(shareIntent,"Share a picture");
  >   ```
  >
  >   ##### 注意：
  >
  >   - 可以使用`*/*`指定MIME类型，但是仅有能够处理一般数据类型的Activity才能匹配到。因为普通的Activity不能详尽所有MIME类型。
  >   - 响应的Activity需要有访问URI的权限。两种方案，一是ContentProvider（per-URI permissions）；二是MediaStore（亦可存储非媒体文件，Android3.0以后。）
  >
  >3. 多块内容，Multiple。同时分享不同的内容，可使用`ACTION_SEND_MULTIPLE`及数据URIs。而MIME需根据内容类型调整，可使用\*符号。如`image/jpeg`、`image/*`、`*/*`。如下：
  >
  >   ```java
  >   ArrayList<Uri> imageUris = new ArrayList<Uri>();
  >   imageUris.add(imageUri1); // Add your image URIs here
  >   imageUris.add(imageUri2);
  >   //接收Activity需要有权限哦
  >   Intent shareIntent = new Intent();
  >   shareIntent.setAction(Intent.ACTION_SEND_MULTIPLE);//复合类型的MIME，Action
  >   shareIntent.putParcelableArrayListExtra(Intent.EXTRA_STREAM, imageUris);//传递数组
  >   shareIntent.setType("image/*");//复合MIME
  >   startActivity(Intent.createChooser(shareIntent, "Share images to.."));
  >   ```

- #### 接收外App传的数据

  > - Activity 在manifest文件 配置，通过Intent Filters来过滤需要处理的数据Action，如下Activity接收单张图片、文本、多张图片时，不同的intent-filer配置：
  >
  >   ```xml
  >   <activity android:name=".ui.MyActivity" >
  >     <!-- 注释，单类型图片的Action过滤，其Action名称不同-->
  >       <intent-filter>
  >           <action android:name="android.intent.action.SEND" />
  >           <category android:name="android.intent.category.DEFAULT" />
  >           <data android:mimeType="image/*" />
  >       </intent-filter>
  >     <!-- 注释，文本类型的Action过滤-->
  >       <intent-filter>
  >           <action android:name="android.intent.action.SEND" />
  >           <category android:name="android.intent.category.DEFAULT" />
  >           <data android:mimeType="text/plain" />
  >       </intent-filter>
  >     <!-- 注释，多种类型图片的Action过滤-->
  >       <intent-filter>
  >           <action android:name="android.intent.action.SEND_MULTIPLE" />
  >           <category android:name="android.intent.category.DEFAULT" />
  >           <data android:mimeType="image/*" />
  >       </intent-filter>
  >   </activity>
  >   ```
  >
  > - 处理接收数据，通过getIntent()来获取extra数据，需要知道传递来的具体类型，结构，做相应处理。有时需要访问权限。若是数据量太大，应考虑避免UI线程的阻塞。

- #### ActionBar 分享功能

  > Android4.0引入ActionProvider，其子类ShareActionProvider用于分享数据。ActionBar步骤：
  >
  > 1. 在Menu的xml中定义`android:actionProviderClass`属性。
  >
  >    ```xml
  >    <menu xmlns:android="http://schemas.android.com/apk/res/android">
  >        <item android:id="@+id/menu_item_share"
  >            android:showAsAction="ifRoom"
  >            android:title="Share"
  >            android:actionProviderClass="android.widget.ShareActionProvider" />
  >      <!--如上则声明该item需要share action provider来匹配内容 -->
  >        ...
  >    </menu>
  >    ```
  >
  > 2. 需要提供intent给ShareActionProvider，示例如下：
  >
  >    ```java
  >    private ShareActionProvider mShareActionProvider;
  >    ...
  >    @Override
  >    public boolean onCreateOptionsMenu(Menu menu) {
  >        //菜单布局文件
  >        getMenuInflater().inflate(R.menu.share_menu, menu);
  >        //加载配有ShareActionProvider属性的item
  >        MenuItem item = menu.findItem(R.id.menu_item_share);
  >        // 实例化ShareActionProvider
  >        mShareActionProvider = (ShareActionProvider) item.getActionProvider();
  >        // 返回true表示显示菜单项
  >        return true;
  >    }
  >    //回调更新intent，用于分享
  >    private void setShareIntent(Intent shareIntent) {
  >        if (mShareActionProvider != null) {
  >            mShareActionProvider.setShareIntent(shareIntent);
  >        }
  >    }
  >    ```

### 2. 分享文件

​	分享文件最为安全的方式是使用content URI，Android中FileProvider有getUriForFile()创建文件content URI。少量数据可以用intent传递。

- #### 建立文件分享

  > 要安全地提供文件分享，需要配置Content URI。
  >
  > 1. 指定FileProvider，在manifest中定义一个provider的entry，声明Authority等。如下示例：
  >
  > ```xml
  > <manifest xmlns:android="http://schemas.android.com/apk/res/android"
  >     package="com.example.myapp">
  >     <application
  >         ...>
  >       <!-- authorities、meta-data的配置 -->
  >         <provider
  >             android:name="android.support.v4.content.FileProvider"
  >             android:authorities="com.example.myapp.fileprovider"
  >             android:grantUriPermissions="true"
  >             android:exported="false">
  >             <meta-data
  >                 android:name="android.support.FILE_PROVIDER_PATHS"
  >                 android:resource="@xml/filepaths" />
  >           <!-- meta-data指定文件共享目录，在res/xml下 -->
  >         </provider>
  >         ...
  >     </application>
  > </manifest>
  > ```
  >
  > 2. 共享文件的目录，res/xml中配置，filepaths.xml，如下示例：
  >
  >    ```xml
  >    <paths>
  >      <!-- 每一个共享目录都是一个item，这里表示共享了files/ 目录下的子目录，files-path这个标签适用于共享应用内部储存，files/下的目录。name=“myimages” 做为content uri中的路径标记-->
  >    	<files-path path="images/" name="myimages"/>
  >    </paths>
  >    ```
  >
  >    `<paths>`有多个子标签，各自代表不同共享目录，`<files-path>`表示内部files/下目录，`<external-path>`外部存储目录，`<cache-path>`缓存目录。参考[FileProvider]()。注意*xml里写的目录，无法在代码中追加和修改*
  >
  >    - Content URI包含`<provider>`指定Authority（“com.example.myapp.fileprovider”）;
  >    - 路径“myimages/”；
  >    - 文件名称。
  >
  >    例如获取上述files/images/下的aa.jpg文件，File Provider提供的URI：
  >
  >    `content://com.example.myapp.fileprovider/myimages/aa.jpg`

- #### 分享文件

  > - 上面创建了共享提供者，此处需要共享请求者。
  >
  > ```java
  >  File requestFile = new File(mImageFilename[position]);
  >                 try {
  >                     fileUri = FileProvider.getUriForFile(
  >                             MainActivity.this,
  >                             "com.example.myapp.fileprovider",
  >                             requestFile);
  >                 } catch (IllegalArgumentException e) {
  >                     Log.e("File Selector",
  >                           "The selected file can't be shared: " +
  >                           clickedFilename);
  >                 }
  > ```
  >
  > *需要注意的是，能获取content uri的文件，都是在manifest文件中，provider下meta-data配置了`<paths>`标签内的文件，否则会抛IllegalArgumentException*
  >
  > - 设置文件授权：
  >
  >   ```java
  >   if(fileUri != null){
  >     //Grant temporary read permission to the content URI，授权具有临时性，一次性。
  >     mResultIntent.addFlags(Intent.FLAG_GRANT_URI_PERMISSION);
  >   }
  >   ```
  >
  >   **Caution:**调用setFlags()授权文件是唯一的安全方法，应避免Context.grantUriPermission(),它需要Context.revokeUriPermission()才能撤销授权。

- #### 请求分享文件

  > 一般文件共享分为共享者与请求者，或者服务器与客户端。服务器需要配置共享清单，客户端需要请求共享，并指定请求类型。
  >
  > -    发送文件请求，客户端startActivityForResult()，通过intent的Action，附带data，MIME去请求服务器的共享数据。服务器来显示对应的共享清单。
  >
  >      ```java
  >      mRequestFileIntent = new Intent(Intent.ACTION_PICK);//Action
  >      mRequestFileIntent.setType("image/jpg");//MIME类型
  >      ...
  >      ```
  >
  > - 在onActivityResult()中处理服务器返回的URI，注：*刚开始只是处理URI，而无任何实际的文件操作和访问，不会影响服务器文件安全*。
  >
  >      ```java
  >                   @Override
  >            public void onActivityResult(int requestCode, int resultCode,
  >                           Intent returnIntent) {
  >                       // If the selection didn't work
  >                       if (resultCode != RESULT_OK) {
  >                           // Exit without doing anything else
  >                           return;
  >                       } else {
  >                           // Get the file's content URI from the incoming Intent
  >                           Uri returnUri = returnIntent.getData();
  >                           /*
  >      * Try to open the file for "read" access using the
  >      * returned URI. If the file isn't found, write to the
  >      * error log and return.
  >      */
  >      try {
  >               /*
  >                * Get the content resolver instance for this context, and use it
  >                * to get a ParcelFileDescriptor for the file.
  >                */
  >               mInputPFD = getContentResolver().openFileDescriptor(returnUri, "r");
  >           } catch (FileNotFoundException e) {
  >               e.printStackTrace();
  >               Log.e("MainActivity", "File not found.");
  >               return;
  >           }
  >           // Get a regular file descriptor for the file,客户端利用FileDescriptor对象类操作文件。
  >           FileDescriptor fd = mInputPFD.getFileDescriptor();
  >           ...
  >      }
  >      ```

- #### 获取文件信息

  > 上一步获取了服务器提供的content uri和file descriptor对象，但并不能操作文件，还需要指导文件信息，如大小、类型。
  >
  > -    获取文件MIME，通过ContentResolver.getType()获取uri对应的文件类型。
  >
  >      ```java
  >      Uri returnUri = returnIntent.getData();
  >      String mimeType = getContentResolver().getType(returnUri);
  >      ```
  >
  > - 获取文件大小，[FileProvider]()的query()方法返回Cuisor对象，包含对应uri的文件名称大小信息。[DISPLAY_NAME]()，[SIZE]()。
  >
  >      ```java
  >             Uri returnUri = returnIntent.getData();
  >                Cursor returnCursor =
  >                        getContentResolver().query(returnUri, null, null, null, null);
  >                /*
  >      * Get the column indexes of the data in the Cursor,
  >      * move to the first row in the Cursor, get the data,
  >      * and display it.
  >      */
  >        int nameIndex = returnCursor.getColumnIndex(OpenableColumns.DISPLAY_NAME);
  >        int sizeIndex = returnCursor.getColumnIndex(OpenableColumns.SIZE);
  >        returnCursor.moveToFirst();
  >      ```

### 3. NFC分享文件

Android Beam文件传输可在设备间传输大文件，API调用方便。

- #### 发送文件

  > 使用NFC发送文件，需要设备支持NFC且app生命NFC和外部存储权限。使用URI给Android Beam来传出文件。需要满足以下要求：
  >
  > 1. `Android版本api>=16。`
  >
  > 2. `传送的文件必须在外部存储上。`
  >
  > 3. 文件必须全局可读，可以用File.setReadable(true,false)来设置。
  >
  > 4. 必须提供文件的File URI。Android Beam无法处理FileProvider.getUriForFile生成的URI。
  >
  >    ```xml
  >    <uses-permission android:name="android.permission.NFC" />
  >    <uses-permission android:name="android.permission.READ_EXTERNAL_STORANGE" />
  >    ```
  >
  >    *其中外部存储权限在4.2.2之前不是必须声明的。*
  >
  >    ```xml
  >    <uses-feature android:name="android.hardware.nfc"
  >                  android:required="true" />
  >    <!-- 如此设置，声明该应用必须要硬件nfc支持才可以运行。若是required为false，则需要检测设备是否支持Android Beam-->
  >    ```
  >
  > - 测试Android Beam，PackageManager.hasSystemFeature()和参数FEATURE_NFC来测是nfc。Build.VERSION.SDK_INT系统版本号。
  >
  >   ```java
  >   boolean hasNFC= PackageManager.hasSystemFeature(PackageManager.FEATURE_NFC);//判断NFC可用与否。
  >   Build.VERSION.SDK_INT<Build.VERSION_CODE.JELLY_BEAN_MR1//版本低。
  >     //可用的话，实例化NfcAdapter
  >     NfcAdapter adapter = NfcAdatper.getDefaultAdapter(this);
  >   ```
  >
  > - 通过回调函数获取数据
  >
  >   ```java
  >   private Uri[] mFileUris = new Uri[10];//提供给AndroidBeam的URIs
  >   private class FileUriCallback implements NfcAdapter.CreateBeamUrisCallback{
  >     public FileUriCallback(){
  >     }
  >     @override
  >     public Uri[] createBeamUris(NfcEvent event){
  >       return mFileUris;
  >     }
  >   }
  >   ```
  >
  >   通过setBeamPushUrisCallback()将回调提供给Android Beam文件传输。
  >
  >   ```java
  >   mFileUriCallback = new FileUriCallback();
  >   mNfcAdapter.setBeamPushUrisCallback(mFileUriCallback,this);
  >   ```
  >
  > - 指定要发送的文件，给文件File URI，然后加入URIs数组。记住需要有文件的读取权限。

- #### 接收文件

  > Android Beam文件传输时，是将文件copy到某特殊目录，然后由Media Scanner扫描文件，在MediaStore Provider中为媒体文件添加条目记录。
  >
  > - 响应传输来的请求，并显示数据。
  >
  >   > Android Beam传输数据到接收设备后，会发送Intent通知，包含ACTION_VIEW,MIME,URI。用户确认通知后，intent被发至系统，寻求其他响应。
  >   >
  >   > Activity在manifest配置`<intent-filter>`加入`<action android:name="android.intent.action.VIEW"/>`、`<category android:name="android.intent.category.CATEGORY_DEFAULT"`、`<data android:nimeType="mime-type"`分别标识不同的Action，category和数据type。
  >
  >   **Action_view的action也不一定就是Android beam发送的。***
  >
  >   ```xml
  >   <activity 
  >     android:name="com.example.android.nfctransfer.ViewActivity"
  >       android:label="Android Beam Viewer">
  >         <intent-filter>
  >           <action android:name="android.intent.action.VIEW"/>
  >           <category android:name="android.intent.category.DEFAULT"/>
  >           ...
  >           </intent-filter>
  >     </activity>
  >   ```
  >
  > - 读取文件需要权限
  >
  >   `<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>`
  >
  >   上面所说，ACTION_VIEW不一定就是AndroidBeam发送的intent，所以需要检查Scheme和Authority。调用Uri.getScheme。
  >
  >   ```java
  >   mIntent = getIntent();
  >   //判断Action，机器Scheme和Authority
  >   Uri beamUri = mIntent.getData();
  >   beamUri.getScheme();//判断是否是“file”，或者“content”
  >   ```
  >
  > - File URI中获取目录
  >
  >   ```java
  >   public String handleFileUri(Uri beamUri) {
  >           // Get the path part of the URI
  >           String fileName = beamUri.getPath();
  >           // Create a File object for this filename
  >           File copiedFile = new File(fileName);
  >           // Get a string containing the file's parent directory
  >           return copiedFile.getParent();
  >       }
  >   ```
  >
  > - Content URI中获取目录，MediaS tore会含有文件的uri信息。Uri.getAuthority()获取authority，返回值MediaStore.AUTHORITY或者其他。
  >
  >   为其他类型时候，不一定可以获取目录；


