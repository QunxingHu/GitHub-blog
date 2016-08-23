---
title: Android Activity间传值方式总结
date: 2016-08-18 15:14:34
tags: 
  - Android
  - Activity
---

#### 1. 使用Bundle,extra 传送简单的类型
数据发送端：
```java
Bundle bundle = new Bundle();
bundle.putString("name", nameStr);
bundle.putDouble("height", heightNum);
intent.putExtras(bundle);
startActivity(intent);
```
<!-- more -->
数据接收端：
```java
Bundle bundle = this.getIntent().getExtras();
String name = bundle.getString("name");
Double height = bundle.getDouble("height");
```

#### 2. 利用static静态数据，public static成员变量

我们千万不要以为Davlik虚拟机的垃圾回收器会帮助我们回收不需要的内存垃圾。事实上，回收器并不可靠，尤其是手机上，是更加的不可靠。 因此，除非我们要使自己的程序变得越来越糟糕，否则**尽量远离static**。

注：如果经常使用static的Bitmap、Drawable等变量。可能就会抛出一个在Android系统中非常著名的异常 `ERROR/AndroidRuntime(4958): Caused by: Java.lang.OutOfMemoryError: bitmap size exceeds VM budget`

#### 3. 基于外部存储的传输 ，File/Preference/Sqlite,如果要针对第三方应用需要Content provider
作为一个完成的应用程序，数据存储操作是必不可少的。因此，Android系统一共提供了四种数据存储方式。分别是：SharePreference、SQLite、Content Provider和File。由于Android系统中，数据基本都是私有的的，都是存放于“data/data/程序包名”目录下，所以**要实现数据共享，正确方式是使用Content Provider**。

**SQLite：**
> SQLite是一个轻量级的数据库，支持基本SQL语法，是常被采用的一种数据存储方式。Android为此数据库提供了一个名为SQLiteDatabase的类，封装了一些操作数据库的API。

**SharedPreference：**
> 除SQLite数据库外，另一种常用的数据存储方式，其本质就是一个xml文件，常用于存储较简单的参数设置。

**File：**
> 即常说的文件（I/O）存储方法，常用语存储大数量的数据，但是缺点是更新数据将是一件困难的事情。

**ContentProvider:**
  ContentProvider是平台中，在不同应用程序之间实现数据共享的一种机制。一个应用程序如果需要让别的程序可以操作自己的数据，即可采用这种机制。并且此种方式忽略了底层的数据存储实现，ContentProvider提供了一种统一的通过Uri实现数据操作的方式。

**详细介绍使用过程:**
File 通过文件内容的读取传递数据
Preference： SharedPreferences也是一种轻型的数据存储方式，它的本质是基于XML文件存储key-value键值对数据，通常用来存储一些简单的配置信息。SharedPreferences对象本身只能获取数据而不支持存储和修改，存储修改是通过Editor对象实现。

**实现SharedPreferences存储的步骤如下：**
>一、根据Context获取SharedPreferences对象
二、利用edit()方法获取Editor对象。
三、通过Editor对象存储key-value键值对数据。
四、通过commit()方法提交数据。
```java
SharedPreferences sp=getSharedPreferences("login",0);//login存储文件名
SharedPreferences.Editor se=sp.edit();;
se.putString("server", logEdit.getText().toString());
se.putString("port", portEdit.getText().toString());
se.commit();
```

**ContentProvider**
>其步骤为：
1. 在当前应用程序中定义一个ContentProvider。
2. 在当前应用程序的AndroidManifest.xml中注册此ContentProvider
3. 其他应用程序通过ContentResolver和Uri来获取此ContentProvider的数据。

  在程序A中，继承ContProvider类，并重写其中的方法
```java
public class MyProvider extends ContentProvider{
      @Override
      public int delete(Uri uri, String selection, String[] selectionArgs) {
          // TODO Auto-generated method stub
          return 0;
      }

      @Override
      public String getType(Uri uri) {
          // TODO Auto-generated method stub
          return null;
      }

      @Override
      public Uri insert(Uri uri, ContentValues values) {
          return null;
      }

      //在Create中初始化一个数据库
      @Override
      public boolean onCreate() {
          SQLiteDatabase db = this.getContext().openOrCreateDatabase("test_db.db3",

Context.MODE_PRIVATE, null);
          db.execSQL("create table tab(_id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT NOT

NULL)");
          ContentValues values = new ContentValues();
          values.put("name", "test");
          db.insert("tab", "_id", values);
          db.close();
          return true;
      }

      //实现query方法
      @Override
      public Cursor query(Uri uri, String[] projection, String selection,
              String[] selectionArgs, String sortOrder) {
          SQLiteDatabase db = this.getContext().openOrCreateDatabase("test_db.db3",

Context.MODE_PRIVATE, null);
          Cursor c = db.query("tab", null, null, null, null, null,null);
          return c;
      }

      @Override
      public int update(Uri uri, ContentValues values, String selection,
              String[] selectionArgs) {
          // TODO Auto-generated method stub
          return 0;
      }
  }
```
 在其AndroidManifest.xml中声明此ContentProvider，其中authorities属性定义了此ContentProvider的Uri标识。
```xml
<provider android:name=".MyProvider" android:authorities="com.test.MyProvider"/>
```
  在应用程序B中，通过ContentResolver获取程序A的ContentProvider中的数据。
```java
public class MainActivity extends Activity {
      @Override
      public void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.main);

         //获取上下文
          Context ctx = MainActivity.this;
          //获取ContentResolver对象
          ContentResolver resolver = ctx.getContentResolver();
          //获取Uri对象
          Uri uri = Uri.parse("content://com.test.MyProvider");
          //获取数据
          Cursor c = resolver.query(uri, null, null, null, null);
          c.moveToFirst();
          for(int i=0; i<c.getCount(); i++){
              int index = c.getColumnIndexOrThrow("name");
              String src = c.getString(index);
              Log.d("", src);
              c.moveToNext();
          }
      }
  }
```
　再观察两个应用程序的结构，A的程序结构，可以清楚看到其有一个名为“test_db.db3”的数据库，B的程序结构，其并没有任何数据库用于存储数据。由此图，可以确定应用程序B中查询出来的数据结果是来自于应用程序A。

以上就是ContentProvider的使用方式，这种存储方式相比SQLite和SharedPreferences，其复杂性是显而易见的，但是在处处可见“云”的今天，程序间的数据交互需求令ContentProvider存储机制变成必不可少的一部分。

#### 4. 基于Ipc的通信机制

context与service之间的传输，如Activity与Service之间的通信

#### 5. 基于Application  Context

在一个activity初始化一个`ArrayList<HashMap<Sting,Map>>`对象，然后经过一个tableactivity，在传递到另外一个activity，一开始直接考虑用putExtra，测试发现数据只能传递一次，就考虑用Application传递Java里面通常是用一个static的变量（例如singleton之类的）来同步activity之间（程序里面类之间）的状态。在android里面比较靠谱的做法是用application context来关联这些状态。每个activity都是context，里面包含了运行时的状态。同样application也有一个context，android会保证这个context是唯一的实例。
``` java
package net.blogjava.mobile1;

import android.app.Application;
 import android.graphics.Bitmap;

public class MyApp extends Application
 {
     private Bitmap mBitmap;

    public Bitmap getBitmap()
     {
         return mBitmap;
     }

    public void setBitmap(Bitmap bitmap)
     {
         this.mBitmap = bitmap;
     }

}
```
``` xml
< application android:name=".MyApp" android:icon="@drawable/icon" android:label="@string/app_name">
</application>
```
获得Bitmap对象的代码：
``` java
ImageView imageview = (ImageView)findViewById(R.id.ivImageView);
MyApp myApp = (MyApp)getApplication();
imageview.setImageBitmap(myApp.getBitmap());
```
上面两段代码可以在任何的Service、Activity中使用。