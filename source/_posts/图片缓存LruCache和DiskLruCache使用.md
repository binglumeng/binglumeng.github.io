---
title: 图片缓存LruCache和DiskLruCache的使用
date: 2017-03-21 15:26
tags:
    - Lrucache
	- Android
categories:
    - Android
---资源
class BitmapWorkerTask extends AsyncTask<Integer, Void, Bitmap> {  
    // 在后台加载图片。  
    @Override  
    protected Bitmap doInBackground(Integer... params) {  
        final Bitmap bitmap = decodeSampledBitmapFromResource(  
                getResources(), params[0], 100, 100);  
        addBitmapToMemoryCache(String.valueOf(params[0]), bitmap);  
        return bitmap;  
    }  
}
```
在此说明一下，以前认为的使用什么软引用、弱引用来保存资源引用的方式，不再提倡，而且也不能保证优化效果了，因为高版本的Android中Java回收机制，会偏向于回收这些引用，从而并不能很好的起到缓存的作用。

##DiskLruCache
DiskLruCache类似于LruCache，其是将资源缓存在外部存储磁盘上，而不是内存，这样就可以有相对更为充足的资源空间，缓存更多的数据。具体的原理介绍，同样烦请移步上面大神的博客。此处仅作简要使用说明：

- 先贴出DiskLruCache中使用到的工具类文件
```java
//DiskLruCache中使用到的工具方法
public class Utils {
    /**
     * 获取缓存文件夹，这里优先选择SD卡下面的android/data/packageName/cache/路径，若没有SD卡，就选择data/data/packageName/cache
     *
     * @param context    上下文环境
     * @param uniqueName 缓存文件夹名称
     * @return 返回缓存文件
     */
    public static File getDiskCacheDir(Context context, String uniqueName) {
        String cachePath;
        if (Environment.MEDIA_MOUNTED.equals(Environment.getExternalStorageState())
                || !Environment.isExternalStorageRemovable()) {
            cachePath = context.getExternalCacheDir().getPath();
        } else {
            cachePath = context.getCacheDir().getPath();
        }
        return new File(cachePath + File.separator + uniqueName);
    }

    /**
     * 获取本App的版本号
     *
     * @param context context上下文
     * @return 返回版本号
     */
    public static int getAppVersion(Context context) {
        try {
            PackageInfo info = context.getPackageManager().getPackageInfo(context.getPackageName(), 0);
            return info.versionCode;
        } catch (PackageManager.NameNotFoundException e) {
            e.printStackTrace();
        }
        return 1;
    }

    /**
     * 给字符串来个md5加密，
     * @param key 需要加密的string
     * @return 返回加密后的string ，或者加密失败，就返回string的哈希值
     */
    public static String hashKeyForDisk(String key) {
        String cacheKey;
        try {
            //md5加密
            MessageDigest mDigest = MessageDigest.getInstance("MD5");
            mDigest.update(key.getBytes());
            cacheKey = bytesToHexString(mDigest.digest());
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
            //若md5加密失败，就用哈希值
            cacheKey = String.valueOf(key.hashCode());
        }
        return cacheKey;
    }

    /**
     * 字节数组转为十六进制字符串
     * @param bytes 字节数组
     * @return 返回十六进制字符串
     */
    private static String bytesToHexString(byte[] bytes) {
        StringBuilder sb = new StringBuilder();
        for (byte b : bytes) {
            String hex = Integer.toHexString(0xFF & b);
            if (hex.length()==1){
                sb.append('0');
            }
            sb.append(hex);
        }
        return sb.toString();
    }
}
```
- DiskLruCache的使用
```java

public class MainActivity extends AppCompatActivity {
    DiskLruCache mDiskLruCache = null;//diskLruCache的对象
    String imgUrl = "http://img.my.csdn.net/uploads/201309/01/1378037235_7476.jpg";//图片链接

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //初始化缓存配置
        openCache();
    }

    /**
     * 初始化缓存配置
     */
    private void openCache() {
        try {
            //缓存图片数据的文件夹
            File cacheDir = Utils.getDiskCacheDir(this, "bitmap");
            if (!cacheDir.exists()) {
                //使用mkdirs可以连同上级文件夹一同创建，否则mkdir可能会报错
                cacheDir.mkdirs();
            }
            //参数，1、缓存目录；2、app版本号，因为它认为版本升级，缓存就没必要保存。3、一个key值对应多少个缓存文件，一般1个。4、单个缓存多大，10M就够了。
            //超过最大缓存限制的，就会被自动清除了，所以一般不用程序中调用removeCache。
            mDiskLruCache = DiskLruCache.open(cacheDir, Utils.getAppVersion(this), 1, 10 * 1024 * 1024);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 写入缓存
     */
    public void writeCache(View view) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                //将图片的url地址md5 加密后，生成的key来作为缓存的唯一标志key，那么就可以实现图片和缓存对应起来。
                String key = Utils.hashKeyForDisk(imgUrl);
                try {
                    DiskLruCache.Editor editor = mDiskLruCache.edit(key);
                    if (editor != null) {
                        //此处传入0参数的含义是，缓存的编号，因为DiskLruCache.open时候，传入了最大缓存个数为1，所以次数就是0就好。
                        OutputStream outputStream = editor.newOutputStream(0);
                        //根据现在成功与否，来决定是否提交缓存
                        if (downloadImage(imgUrl, outputStream)) {
                            editor.commit();
                        } else {
                            editor.abort();
                        }
                    }
                    //刷新，写入
                    mDiskLruCache.flush();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }


    /**
     * 下载图片
     *
     * @param imgUrl       图片网址链接
     * @param outputStream 输出流对象
     * @return 返回时候完成下载成功
     */
    private boolean downloadImage(String imgUrl, OutputStream outputStream) {
        HttpURLConnection urlConnection = null;
        BufferedOutputStream out = null;
        BufferedInputStream in = null;

        try {
            URL url = new URL(imgUrl);
            urlConnection = (HttpURLConnection) url.openConnection();
            in = new BufferedInputStream(urlConnection.getInputStream(), 8 * 1024);//Buffer输入流，8M大小的缓存
            out = new BufferedOutputStream(outputStream, 8 * 1024);
            int b;//正在读取的byte
            while ((b = in.read()) != -1) {
                out.write(b);
            }
            return true;
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        //关闭资源
        finally {
            if (urlConnection != null) {
                urlConnection.disconnect();
            }
            if (out != null) {
                try {
                    out.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (in != null) {
                try {
                    in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return false;
    }

    /**
     * 读取缓存
     *
     * @param view
     */
    public void readCache(View view) {
        //使用DiskLruCache获取缓存，需要传入key，而key是imageUrl加密后的字符串，
        String key = Utils.hashKeyForDisk(imgUrl);
        try {
            //通过key获取的只是一个快照，需要从快照获取输入流，转化为数据对象
            DiskLruCache.Snapshot snapshot = mDiskLruCache.get(key);
            if (snapshot != null) {
                InputStream inputStream = snapshot.getInputStream(0);//类似写缓存时候，传入的是缓存的编号
                //可以使用bitmapFactory
                Drawable drawable = Drawable.createFromStream(inputStream, "drawable");
                ImageView imageView = (ImageView) findViewById(R.id.iv_cache);
                imageView.setImageDrawable(drawable);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    /**
     * 清除缓存
     *
     * @param view
     */
    public void removeCache(View view) {
        String key = Utils.hashKeyForDisk(imgUrl);
        try {
            //清除指定key的缓存
            mDiskLruCache.remove(key);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 演示DiskLruCache的其他api
     */
    public void otherAPI() {
        //缓存目录大小
        mDiskLruCache.size();
        //将内存中的操作记录，同步到日志文件（journal），一般不要频繁操作，在Activity的onPause中调用于此就好。
        try {
            mDiskLruCache.flush();
        } catch (IOException e) {
            e.printStackTrace();
        }
        //关闭缓存,一般在Activity的onDestroy中调用就好
        try {
            mDiskLruCache.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        //清空缓存，不同于remove，这是清空全部缓存
        try {
            mDiskLruCache.delete();
        } catch (IOException e) {
            e.printStackTrace();
        }

        /*
         * journal文件分析
         * 前五行基本就是open相关的参数的配置信息
         * 第六行开始，DIRTY开头的，表示脏数据记录，每次调用DiskLruCache.edit都会有一个记录，
         * 调用commit时候，会写入CLEAN记录，而调用abort，则写入REMOVE记录。
         * 日志里item还会记录缓存的大小。READ就是调用get时候写入的记录。journal的记录不知无止境的，2000条左右计数，就会重构。
         */
    }
```

单独的使用内存缓存或者外部缓存，都未必是是最好的，一个优秀的开发者，必然会考虑到两者的结合使用。上面大神也有提供一个演示用的demo，在其博客中也有简介。