###隐式Intent

* 有两个标签，一个action，一个category。  如果启动其他程序的活动，要setData（添加data标签）。

*  构建好隐式intent后，启动intent，系统自动判断启动同时满足action、catagory、data标签的活动
  不加自定义category时，就是默认android.intent.category.DEFAULT。



*  每个intent可以addCategory()多个catagory，只能指定一个action

**例1.隐式intent启动其他程序**
```xml
//Manifest
<activity android:name=".Main2Activity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:scheme="http"/>
            </intent-filter>
        </activity>
```
```java
public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_VIEW);
                intent.setData(Uri.parse("http://www.baidu.com"));
                startActivity(intent);
                }

public void onClick(View view) {
    Intent i = new Intent(Intent.ACTION_SEND);
    i.setType("text/plain");
    //消息内容
    i.putExtra(Intent.EXTRA_TEXT, getCrimeReport());
    //主题
    i.putExtra(Intent.EXTRA_SUBJECT, getString(R.string.crime_report_subject));
    
    //创建一个选择器显示相应隐式intent的全部activity
    //主要用于每次都强行打开activity选择器并修改提示标题
    i = Intent.createChooser(i, getString(R.string.send_crime_report));

    startActivity(i);
                            }
```

**例2.自定义intent**
在Manifest中
```xml
//Manifest
<activity android:name=".Main2Activity">
            <intent-filter>
                <action android:name="com.example.a6100890.note.ACTION_START"/>
                <category android:name="com.example.a6100890.note.MY_CATEGORY"/>
            </intent-filter>
        </activity>

```

```java
  Intent intent = new Intent("com.example.a6100890.note.ACTION_START");
  intent.addCategory("com.example.a6100890.note.MY_CATEGORY");
  startActivity(intent);

  

```


****



## 隐式Intent的组成

* 要执行的操作：通常以Intent类的常量来表示
* 待访问数据的位置：可能是某个网页或文件的URL，或者ContentProvider中某个内容URI
* 操作涉及的数据类型：如果一个intent包含数据位置，通常可以从中推测出数据的类型
* 可选类别category：描述何时用什么方式使用某个activity
