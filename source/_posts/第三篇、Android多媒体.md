---
title: "Android多媒体"
date: 2017-03-27 16:57
author: 冰路梦
tag:
    - Android
categories:
    - Android
---
## 第三章、Android多媒体

### 1. 管理音频播放

- #### 控制音量与音频播放

  应用若使用音频功能，应保证App获取音频焦点，不造成多个应用声音混杂，且可响应音频按钮事件。

  > - Android有播放音乐、闹铃、通知、来电等等不同的音频流，需要独立鉴别。
  >
  >   音量按钮会调节当前音频流，若无，则调节响铃。Android 中`setVolumeControlStream()`方法控制音频流。一般在Activity或Fragment的`onCreate()`中调用它。如：
  >
  > ```java
  > setVolumeControlStream(AudioManager.STREAM_MUSIC);
  > ```
  >
  > - **响应按键事件**，硬件的音控按钮会激活系统广播`ACTION_MEDIA_BUTTON`的Intent，App需要有receiver在manifest中：
  >
  > ```xml
  > <receiver android:name=".RemoteControlReceiver">
  > 	<intent-filter>
  >   		<action android:name="android.intent.action.MEDIA_BUTTON"/>
  >   	</intent-filter>
  > </receiver>
  > ```
  >
  > - *Receiver接收广播，过滤Action，可以通过`EXTRA_KEY_EVENT`区分按钮*，如：
  >
  > ```java
  > public class RemoteControlReceiver extends BroadcastReceiver{
  >   @override
  >   public void onReceive(Context context,Intent intent){
  >     KeyEvent event = (KeyEvent)intent.getParcelableExtra(Intent,EXTRA_KEY_EVENT);
  >     if(KeyEvent.KEYCODE_MEDIA_PLAY==event.getKeyCode()){
  >       //处理按钮Play的点击事件
  >       ...
  >     }
  >   }
  > }
  > ```
  >
  > `注意，可能有多个程序监听按钮`，可以通过AudioManager管理App注册监听与取消。
  >
  > ```java
  > AudioManager am = mContext.getSystemService(Context.AUDIO_SERVICE);
  > ...
  >   //开启监听
  > am.registerMediaButtonEventReceiver(RemoteControlReceiver);
  > ...
  >   //取消监听
  > am.unregisterMediaButtonEventReceiver(RemoteControlReceiver);
  > ```
  >
  > **音频的控制，并不一定是Activity可见不可见来决定**，正确的方法是判断App获取/失去音频流焦点的状态。

- #####  管理音频焦点

  Android中只有获取音频流焦点的App方能控制音频。

  - 注意点：1、请求焦点；2、获取焦点；3、监控焦点状态，并作相应处理。

  > ```java
  > requestAudioFocus();//请求焦点，成功则返回AUDIOFOCUS_REQUEST_GRANTED
  > //需要制定当前音频流，并明确焦点获取是临时`Transient`，还是永久`Permanent`。
  > AudioManager am = mContext.getSystemService(Context.AUDIO_SERVICE);
  > ...
  > // 请求音频焦点，指定当前音频流为music，传入请求参数。
  > int result = am.requestAudioFocus(afChangeListener,AudioManager.STREAM_MUSIC,AudioManager.AUDIOFOCUS_GAIN);
  > if (result == AudioManager.AUDIOFOCUS_REQUEST_GRANTED) {  am.registerMediaButtonEventReceiver(RemoteControlReceiver);
  >     // 开始播放
  > }
  > ...
  > //释放焦点的方法
  >   am.abandonAudioFocus(afChangeListener);
  > ```
  >
  > 一旦结束播放，确保调用`abandonAudioFocus()`来释放焦点和监听AudioManager.OnAudioFocusChangeListener。
  >
  > - **在使用临时焦点时候，可选择设置`Ducking`开启，则其他音频流不会停止，而只是变为背景音**
  >
  > ```java
  > int result = am.requestAudioFocus(afChangeListener,AudioManager.STERAM_MUSIC,AudioManager.AUDIOFOCUS_GAIN_TRANSIENT_MAY_DUCK);//开启Ducking
  > if(result == AudioManager.AUDIOFOCUS_REQUEST_GRANTED){
  >   //paly music And other app maybe playing too。
  > }//若其他app也是Ducking，则本App可以监听它的焦点状态。
  > ```
  >
  > _音频焦点状态变化的监听_ `onAudioFocusChange()`，三种状态，永久，临时，Ducking式。
  >
  > ```java
  > OnAudioFocusChangeListener afChangeListener = new OnAudioFocusChangeListener(){
  >   public void onAudioFocusChange(int focusChange){
  >     if(focusChange == AUDIOFOCUS_LOSS_TRANSIENT){
  >       //暂停播放
  >       
  >     }else if(focusChange == AudioManger.AUDIOFOCUS_GAIN){
  >       //重新播放
  >     }else if(focusChange == AudioManager.AUDIOFOCUS_LOSS){
  >      am.unregisterMediaButtonEventReceiver(RemoteControlReceiver);
  >       am.abandonAudioFocus(afChangeListener);
  >       //停止播放
  >     }
  >   }
  > }
  > ```
  >
  > - **Duck！**
  >
  > 对音频流使用Ducking状态，会是之变为背景式音频。
  >
  > ```java
  > OnAudioFocusChangeListener afChangeListener = new OnAudioFocusChangeListener() {
  >     public void onAudioFocusChange(int focusChange) {
  >         if (focusChange == AUDIOFOCUS_LOSS_TRANSIENT_CAN_DUCK) {
  >             //Ducking 状态，降低音量，背景音播放
  >         } else if (focusChange == AudioManager.AUDIOFOCUS_GAIN) {
  >             // 恢复正常音量播放
  >         }
  >     }
  > };
  > ```

- ###### 兼容音频输出设备

  检测正使用的硬件设备:`Audio Manager`

  > ```java
  > if (isBluetoothA2dpOn()) {
  >     // Adjust output for Bluetooth.
  > } else if (isSpeakerphoneOn()) {
  >     // Adjust output for Speakerphone.
  > } else if (isWiredHeadsetOn()) {
  >     // Adjust output for headsets
  > } else { 
  >     // If audio plays and noone can hear it, is it still playing?
  > }
  > ```
  >
  > 当音频设备变化时候，要监听改变`ACTION_AUDIO_BECOMING_NOISY`系统广播的intent。App需要有receiver
  >
  > ```java
  > private class NoisyAudioStreamReceiver extends BroadcastReceiver{
  >   @override
  >   public void onReceive(Context context,Intent intent){
  >     if(AudioManager.ACTION_AUDIO_BECOMING_NOISY.equals(intent.getAction())){
  >      //接收到音频输出设备变化，暂停播放 
  >     }
  >   }
  > }
  > private IntentFilter intentFilter = new IntentFilter(AudioManager.ACITON_AUDIO_BECOMING_NOISY);
  > private void startPlayback() {
  >     registerReceiver(myNoisyAudioStreamReceiver(), intentFilter);
  > }
  >
  > private void stopPlayback() {
  >     unregisterReceiver(myNoisyAudioStreamReceiver);
  > }
  > ```

### 2.拍照

- #### 简单的拍照

  >1、请求相机权限
  >
  >```xml
  ><manifest ...>
  >  <!-- 在清单文件中加入该属性，向用户声明本App需要相机权限。 -->
  >	<uses-feature android:name="android.hardware.camera"
  >                  android:required="true"/>
  >  <!-- 自动聚焦 -->
  >   <uses-feature android:name="android.hardware.camera.autofocus" />
  >  <!-- 调用相机需要的权限 -->
  >  
  >  <uses-permission android:name="android.permission.CAMERA" />
  ></manifest>
  >```
  >
  >本App若非必须有相机，`required`可以设置false。代码中可以用`hasSystemFeature(PackageManager.FEATURE_CAMERA)`来检查是否有camera硬件。
  >
  >2、调用系统相机拍照。
  >
  >```java
  >static final int REQUEST_IMAGE_CAPTURE=1;//请求码
  >private void dispatchTakePictureIntent(){
  >  //调用相机的intent
  >  Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
  >  //检查有没有能处理该intent的Activity，以免下面startActivity会空指针，而崩溃
  >  if(takePictureIntent.resolveActivity(getPackageManager()) !=null){
  >    startActivityForResult(takePictureIntent,REQUEST_IMAGE_CAPTURE);
  >  }
  >}
  >```
  >
  >3、获取缩略图
  >
  >Android相机将拍摄好的照片缩小为Bitmap，返回给调用的activity，`key-value`键值对形式将数据绑定到intent返回。`data` key值
  >
  >```java
  >@Override
  >protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  >    if (requestCode == REQUEST_IMAGE_CAPTURE && resultCode == RESULT_OK) {
  >        Bundle extras = data.getExtras();//获取bundle对象
  >        Bitmap imageBitmap = (Bitmap) extras.get("data");//key值，获取缩略图
  >        mImageView.setImageBitmap(imageBitmap);
  >    }
  >}
  >```
  >
  >4、获取全尺寸照片
  >
  >Android一般会保存原始照片数据到指定文件夹下。通常为`DIRECTORY_PICTURES`对应的文件目录，需要读写SD卡的权限。
  >
  >```xml
  ><manifest ...>
  >	<uses_permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
  >  	<!-- 一般有写入权限，就默认会有读取权限了 -->
  ></manifest>
  >```
  >
  >`getExternalFilesDir()`私有目录，`getExternalStoragePublicDirectory()`共有目录，接收参数`DIRECTORY_PICTURES`标明是图片文件夹。
  >
  >**注意文件的保存，需要防止命名冲突，一般会加入时间戳来避免该问题。**
  >
  >```java
  >String mCurrentPhotoPath;
  >
  >private File createImageFile() throws IOException {
  >    // 创建图片文件
  >    String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
  >    String imageFileName = "JPEG_" + timeStamp + "_";
  >    File storageDir = Environment.getExternalStoragePublicDirectory(
  >            Environment.DIRECTORY_PICTURES);
  >    File image = File.createTempFile(
  >        imageFileName,  /* prefix */
  >        ".jpg",         /* suffix */
  >        storageDir      /* directory */
  >    );
  >
  >    // Save a file: path for use with ACTION_VIEW intents
  >    mCurrentPhotoPath = "file:" + image.getAbsolutePath();
  >    return image;
  >}
  >```
  >
  >使用如上方法，来创建新的照片文件：
  >
  >```java
  >static final int REQUEST_TAKE_PHOTO = 1;
  >
  >private void dispatchTakePictureIntent() {
  >    Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
  >    // 判断是否有可以处理拍照的Activity
  >    if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
  >        // 创建照片文件
  >        File photoFile = null;
  >        try {
  >            photoFile = createImageFile();
  >        } catch (IOException ex) {
  >            // 创建文件异常
  >            ...
  >        }
  >        // 照片创建成功的话，完成照片存储。
  >        if (photoFile != null) {
  >            takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT,
  >                    Uri.fromFile(photoFile));
  >            startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
  >        }
  >    }
  >}
  >```
  >
  >5、如果照片目录不是私有，那么需要通知系统，将照片显示到公开目录中,让mediaScanner可以扫描到。
  >
  >```java
  >private void galleryAddPic() {
  >  //intent
  >    Intent mediaScanIntent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
  >    File f = new File(mCurrentPhotoPath);
  >    Uri contentUri = Uri.fromFile(f);
  >  //发送广播，
  >    mediaScanIntent.setData(contentUri);
  >    this.sendBroadcast(mediaScanIntent);
  >}
  >```
  >
  >6、图片缩放
  >
  >多数情况下不需要全尺寸的清晰图片显示，也为了避免内存消耗，使用图片缩放：
  >
  >```java
  >private void setPic() {
  >    // 1、获取需要显示图片的view控件的大小。
  >    int targetW = mImageView.getWidth();
  >    int targetH = mImageView.getHeight();
  >
  >    // 2、获取需要显示的图片的尺寸
  >    BitmapFactory.Options bmOptions = new BitmapFactory.Options();
  >    bmOptions.inJustDecodeBounds = true;
  >    BitmapFactory.decodeFile(mCurrentPhotoPath, bmOptions);
  >    int photoW = bmOptions.outWidth;
  >    int photoH = bmOptions.outHeight;
  >
  >    // 3、计算缩放比，根据宽高
  >    int scaleFactor = Math.min(photoW/targetW, photoH/targetH);
  >
  >    // 4、缩放图片
  >    bmOptions.inJustDecodeBounds = false;//
  >    bmOptions.inSampleSize = scaleFactor;//缩放比
  >    bmOptions.inPurgeable = true;//
  >	//完成图片缩放
  >    Bitmap bitmap = BitmapFactory.decodeFile(mCurrentPhotoPath, bmOptions);
  >    mImageView.setImageBitmap(bitmap);
  >}
  >```

- #### 简单的录像

  > 1、请求相机权限
  >
  > ```xml
  > <manifest ... >
  >     <uses-feature android:name="android.hardware.camera"
  >                   android:required="true" />
  >    <uses-feature android:name="android.hardware.camera.autofocus" />
  >     ...
  > </manifest>
  > ```
  >
  > 2、通过Intent来录制视频,Action是`MediaStore.ACTION_VIDEO_CAPTURE`
  >
  > ```java
  > static final int REQUEST_VIDEO_CAPTURE = 1;
  >
  > private void dispatchTakeVideoIntent() {
  >     Intent takeVideoIntent = new Intent(MediaStore.ACTION_VIDEO_CAPTURE);
  >   //判断是否有可用录像程序
  >     if (takeVideoIntent.resolveActivity(getPackageManager()) != null) {
  >         startActivityForResult(takeVideoIntent, REQUEST_VIDEO_CAPTURE);
  >     }
  > }
  > ```
  >
  > 3、接收返回的视频数据
  >
  > ```java
  > @Override
  > protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  >     if (requestCode == REQUEST_VIDEO_CAPTURE && resultCode == RESULT_OK) {
  >       //获取视频文件保存的uri
  >         Uri videoUri = intent.getData();
  >         mVideoView.setVideoURI(videoUri);
  >     }
  > }
  > ```

- #### 控制相机硬件

  > 1、此处提供给有特殊需要的app来调用Camera硬件，实现自定义的拍照与录像功能。
  >
  > - `onCreate()`中开启线程打开相机。
  >
  > - 或者在`onResume()`中开启相机
  >
  > - 要检测Camera是否可用，被占用
  >
  >   ```java
  >   private boolean safeCameraOpen(int id) {
  >       boolean qOpened = false;
  >       try {
  >         //先释放，再打开，在此捕获异常
  >           releaseCameraAndPreview();
  >           mCamera = Camera.open(id);//相机id，默认后置摄像头。
  >           qOpened = (mCamera != null);
  >       } catch (Exception e) {
  >           Log.e(getString(R.string.app_name), "failed to open Camera");
  >           e.printStackTrace();
  >       }
  >
  >       return qOpened;    
  >   }
  >   //释放相机资源
  >   private void releaseCameraAndPreview() {
  >       mPreview.setCamera(null);
  >       if (mCamera != null) {
  >           mCamera.release();
  >           mCamera = null;
  >       }
  >   }
  >   ```
  >
  > 2、创建相机预览界面，使用`SurfaceView`实现
  >
  > ```java
  > class Preview extends ViewGroup implements SurfaceHolder.Callback {
  >
  >     SurfaceView mSurfaceView;//surface view
  >     SurfaceHolder mHolder;// surface holder
  > 	//构造函数，初始化数据
  >     Preview(Context context) {
  >         super(context);
  >
  >         mSurfaceView = new SurfaceView(context);
  >         addView(mSurfaceView);
  >
  >         // 注册surfaceholder的callback，监控surfaceView的创建与销毁。
  >         mHolder = mSurfaceView.getHolder();
  >         mHolder.addCallback(this);
  >         mHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
  >     }
  > ...
  > }
  > ```
  >
  > ==Preview类的对象，必须在开始预览之前就传递给Camera对象。==
  >
  > $Camera 和Preview必须依照特定顺序来创建$。首先创建Camera对象，示例：
  >
  > ```java
  > public void setCamera(Camera camera) {
  >     if (mCamera == camera) { return; }
  > 	//停止预览，释放camera对象
  >     stopPreviewAndFreeCamera();
  > 	//重新引用camera
  >     mCamera = camera;
  > 	//对象非空时候，开启预览
  >     if (mCamera != null) {
  >         List<Size> localSizes = mCamera.getParameters().getSupportedPreviewSizes();
  >         mSupportedPreviewSizes = localSizes;
  >         requestLayout();
  >         try {
  >             mCamera.setPreviewDisplay(mHolder);
  >         } catch (IOException e) {
  >             e.printStackTrace();
  >         }
  >         // 重要：开启预览，才能拍照。
  >         mCamera.startPreview();
  >     }
  > }
  > ```
  >
  > 3、修改相机设置
  >
  > 由于是控制camera硬件，可以设置拍照方式、曝光补偿等。
  >
  > ```java
  > public void surfaceChanged(SurfaceHolder holder, int format, int w, int h) {
  >     // 设置预览大小
  >     Camera.Parameters parameters = mCamera.getParameters();
  >     parameters.setPreviewSize(mPreviewSize.width, mPreviewSize.height);
  >     requestLayout();
  >     mCamera.setParameters(parameters);
  > 	
  >     //先开启预览，才能调用拍照。
  >     mCamera.startPreview();
  > }
  > //set CameraDisplayOrientation()设置预览方向，横竖屏。
  > ```
  >
  > 4、拍照
  >
  > 预览后，才能调用拍照。`Camera.takePicture()`方法。创建`Camera.PictureCallback`和`Camera.ShutterCallback`对象，传递给`Camera.takePicture()`。
  >
  > 若要连续拍摄，创建`Camera.PreviewCallback`实现`onPreviewFrame()`方法。如此可以拍摄选定的预览帧，或调用`takePicture()`建立延迟。
  >
  > 5、重启Preview
  >
  > ==拍照后，需要重启预览==
  >
  > ```java
  > @Override
  > public void onClick(View v) {
  >     switch(mPreviewState) {
  >     case K_STATE_FROZEN://预览
  >         mCamera.startPreview();
  >         mPreviewState = K_STATE_PREVIEW;
  >         break;
  >     default://拍照
  >         mCamera.takePicture( null, rawCallback, null);
  >         mPreviewState = K_STATE_BUSY;
  >     } // switch
  >     shutterBtnConfig();
  > }
  > ```
  >
  > 6、停止预览并释放相机
  >
  > 使用Camera后，必须释放资源，以备下次调用或者其他应用使用。一般在Surface被毁后，释放预览和相机。
  >
  > ```java
  > public void surfaceDestroyed(SurfaceHolder holder) {
  >     // Surface将会销毁，需在此停止预览
  >     if (mCamera != null) {
  >         // 停止预览
  >         mCamera.stopPreview();
  >     }
  > }
  > /**
  >  * 释放Camera资源
  >  */
  > private void stopPreviewAndFreeCamera() {
  >     if (mCamera != null) {
  >         //停止预览
  >         mCamera.stopPreview();
  >         //重要，停止使用Camera后，必须释放对象资源，在onPause()释放，onResume()重启。
  >         mCamera.release();
  >         mCamera = null;
  >     }
  > }
  > ```

### 3、打印

Android支持创建pdf文件，打印图片，html和文字。

- #### 打印照片

  > *PrintHelper*类打印图片，AndroidSupportLibrary提供的类库。
  >
  > 1、打印一幅图片
  >
  > `setScaleMode()`方法，接收两个选项之一：
  >
  > - SCALE_MODE_FIT,图片适应打印纸
  > - SCALE_MODE_FILL,充满整个纸张，可能会与部分图片无法显示出来。
  >
  > ```java
  > private void doPhotoPrint(){
  >   PrintHelper photoPrinter = new PrintHelper(getActivity());
  >   photoPrinter.setScaleMode(PrintHelper.SCALE_MODE_FIT);
  >   Bitmap bitmap = BitmapFactory.decodeResource(getResource(),R.drawable.iclauncher);
  >   photoPrinter.printBitmap("icLaunchetr.jpg 测试打印",bitmap);
  > }
  > ```

- #### 打印html文档

  > android提供了html文档方式，来实现更为丰富的文本打印输出。
  >
  > 1、加载Html文档
  >
  > 使用`webView`加载html资源，`webview`被作为activity布局的一部分，若是app没有用到该view，则需要创建该对象，来实现html文档：
  >
  > - 加载html文档后，创建一个`WebViewClient`对象，来启动打印任务。
  > - 加载html到`WebView`对象中。
  >
  > ```java
  > private WebView mWebView;
  > private void doWebViewPrint(){
  >   //创建webview对象，用于打印
  >   WebView webView = new WebView(getActivity());
  >   webView.setWebViewClient(new WebViewClient(){
  >     public boolean shouldOverrideUrlLoading(WebView view,String url){
  >       return false;
  >     }
  >     @override
  >     public void onPageFinished(WebView view ,String url){
  >       Log.i(TAG,"html页面加载完毕"+url);
  >       //调用打印，加载完毕后才调用，否则会不完整，或者失败。
  >       createWebPrintJob(view);
  >       mWebView = null;
  >     }
  >   });
  >   //创建一个html文档
  >     String htmlDocument = "<html><body><h1>Test Content</h1><p>Testing, " +
  >             "testing, testing...</p></body></html>";
  >     webView.loadDataWithBaseURL(null, htmlDocument, "text/HTML", "UTF-8", null);
  >
  >     // 保持web View的对象引用，知道适配器打印完毕。
  >     mWebView = webView;
  > }
  > //要是html需要包含图像，放在“assets/”目录下,指定URL，
  > webView.loadDataWithBaseURL("file:///android_asset/images/",htmlBody,"text/HTML","utf-8",null);
  > //或者加载一个网页，需要网络权限。
  > webView.loadUrl("http://developer.android.com/about/index.html");
  > ```
  >
  > ==Web View打印文档会有限制：==
  >
  > - 不能添加页眉、页脚、页码
  > - 不能指定打印页码范围
  > - 一个Web View对象，只能同时处理一个任务。
  > - 不支持html的css属性。
  > - html的javaScript无法调用打印。
  >
  > 2、创建打印任务
  >
  > ```java
  > private void createWebPrintJob(WebView webView) {
  >     // 获取Print Manager实例
  >     PrintManager printManager = (PrintManager) getActivity()
  >             .getSystemService(Context.PRINT_SERVICE);
  >     // 获取PrintAdapter实例
  >     PrintDocumentAdapter printAdapter = webView.createPrintDocumentAdapter();
  >     // 创建打印任务，传递给Printadapter，
  >     String jobName = getString(R.string.app_name) + " Document";
  >     PrintJob printJob = printManager.print(jobName, printAdapter,
  >             new PrintAttributes.Builder().build());
  >     // 完成任务配置，加入打印列表。
  >     mPrintJobs.add(printJob);
  > }
  > ```

- #### 打印自定义文档

  > 1、连接打印管理器，来创建打印任务
  >
  > ```java
  > private void doPrint() {
  >     // Get a PrintManager instance
  >     PrintManager printManager = (PrintManager) getActivity()
  >             .getSystemService(Context.PRINT_SERVICE);
  >     // Set job name, which will be displayed in the print queue
  >     String jobName = getActivity().getString(R.string.app_name) + " Document";
  >     // Start a print job, passing in a PrintDocumentAdapter implementation
  >     // to handle the generation of a print document
  >     printManager.print(jobName, new MyPrintDocumentAdapter(getActivity()),
  >             null); //最后的参数是PrintAttributes，打印机属性设置。
  > }
  > ```
  >
  > 2、创建打印机适配器
  >
  > `PrintDocumentAdapter`负责打印生命周期，
  >
  > - onStart():开始打印，非必需实现的方法，因为总会被调用。
  > - onLayout(): 用户设置页面布局，尺寸之类的，会调用该函数。
  > - onWrite(): 将打印文件渲染成待打印状态，可以在onLayout()中多次调用该方法。
  > - onFinish(): 完成打印，非必需实现。
  >
  > 调用onLayout()和onWrite()，尽量写入一个异步进程中，因为可能耗时。
  >
  > 3、计算打印文档信息
  >
  > 在Print Document Adapter的实现时，需要指定文档类型，计算页数，尺寸之类的信息。
  >
  > 在onLayout()中计算这些数据，可从PrintDocumentInfo中获取。
  >
  > ```java
  > @Override
  > public void onLayout(PrintAttributes oldAttributes,
  >                      PrintAttributes newAttributes,
  >                      CancellationSignal cancellationSignal,
  >                      LayoutResultCallback callback,
  >                      Bundle metadata) {
  >     // Create a new PdfDocument with the requested page attributes
  >     mPdfDocument = new PrintedPdfDocument(getActivity(), newAttributes);
  >
  >     // Respond to cancellation request
  >     if (cancellationSignal.isCancelled() ) {
  >         callback.onLayoutCancelled();
  >         return;
  >     }
  >
  >     // Compute the expected number of printed pages
  >     int pages = computePageCount(newAttributes);
  >
  >     if (pages > 0) {
  >         // Return print information to print framework
  >         PrintDocumentInfo info = new PrintDocumentInfo
  >                 .Builder("print_output.pdf")
  >                 .setContentType(PrintDocumentInfo.CONTENT_TYPE_DOCUMENT)
  >                 .setPageCount(pages);
  >                 .build();
  >         // Content layout reflow is complete
  >         callback.onLayoutFinished(info, true);
  >     } else {
  >         // Otherwise report an error to the print framework
  >         callback.onLayoutFailed("Page count calculation failed.");
  >     }
  > }
  > ```
  >
  > `onLayout()`方法返回结果：完成、取消、失败。必须通过调用PrintDocumentAdapter.LayoutResultCallback对象中的方法指定结果。在`onLayoutFinished()`方法中==boolean的参数==，指明是否与上次布局不同，来决定时候再次调用`onWrite()`方法。
  >
  > `onLayout()`计算文档相关数据
  >
  > ```java
  > private int computePageCount(PrintAttributes printAttributes) {
  >     int itemsPerPage = 4; // default item count for portrait mode
  >
  >     MediaSize pageSize = printAttributes.getMediaSize();
  >     if (!pageSize.isPortrait()) {
  >         // Six items per page in landscape orientation
  >         itemsPerPage = 6;
  >     }
  >
  >     // Determine number of print items
  >     int printItemCount = getPrintItemCount();
  >
  >     return (int) Math.ceil(printItemCount / itemsPerPage);
  > }
  > ```
  >
  > 4、将打印文档写入文件
  >
  > 如下代码展示使用`PrintedPdfDocument`类创建pdf文档的基本原理
  >
  > ```java
  > @Override
  > public void onWrite(final PageRange[] pageRanges,
  >                     final ParcelFileDescriptor destination,
  >                     final CancellationSignal cancellationSignal,
  >                     final WriteResultCallback callback) {
  >     // Iterate over each page of the document,
  >     // check if it's in the output range.
  >     for (int i = 0; i < totalPages; i++) {
  >         // Check to see if this page is in the output range.
  >         if (containsPage(pageRanges, i)) {
  >             // If so, add it to writtenPagesArray. writtenPagesArray.size()
  >             // is used to compute the next output page index.
  >             writtenPagesArray.append(writtenPagesArray.size(), i);
  >             PdfDocument.Page page = mPdfDocument.startPage(i);
  >
  >             // check for cancellation
  >             if (cancellationSignal.isCancelled()) {
  >                 callback.onWriteCancelled();
  >                 mPdfDocument.close();
  >                 mPdfDocument = null;
  >                 return;
  >             }
  >
  >             // Draw page content for printing
  >             drawPage(page);
  >
  >             // Rendering is complete, so page can be finalized.
  >             mPdfDocument.finishPage(page);
  >         }
  >     }
  >
  >     // Write PDF document to file
  >     try {
  >         mPdfDocument.writeTo(new FileOutputStream(
  >                 destination.getFileDescriptor()));
  >     } catch (IOException e) {
  >         callback.onWriteFailed(e.toString());
  >         return;
  >     } finally {
  >         mPdfDocument.close();
  >         mPdfDocument = null;
  >     }
  >     PageRange[] writtenPages = computeWrittenPages();
  >     // Signal the print framework the document is complete
  >     callback.onWriteFinished(writtenPages);
  >
  >     ...
  > }
  > ```
  >
  > Pdf文档生成
  >
  > ```java
  > private void drawPage(PdfDocument.Page page) {
  >     Canvas canvas = page.getCanvas();
  >
  >     // units are in points (1/72 of an inch)
  >     int titleBaseLine = 72;
  >     int leftMargin = 54;
  >
  >     Paint paint = new Paint();
  >     paint.setColor(Color.BLACK);
  >     paint.setTextSize(36);
  >     canvas.drawText("Test Title", leftMargin, titleBaseLine, paint);
  >
  >     paint.setTextSize(11);
  >     canvas.drawText("Test paragraph", leftMargin, titleBaseLine + 25, paint);
  >
  >     paint.setColor(Color.BLUE);
  >     canvas.drawRect(100, 100, 172, 172, paint);
  > }
  > ```
  >
  > **注意，canvas绘图使用point为单位**

