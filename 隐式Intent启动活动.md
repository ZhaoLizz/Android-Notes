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
