# ToolBar UI视图

1. 隐藏默认的ActionBar视图：打开manifest中AppTheme改为NoActionBar
2. 在activity_main.xml中添加Toolbar的标签
3. 在主活动中动态添加Toolbar

```xml
//AppTheme
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

</resources>
```

```xml
//activity_main
<android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:background="?attr/colorPrimary"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        app:popupTheme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize">

    </android.support.v7.widget.Toolbar>
```

```java
//mainActivity中动态加载布局
Toolbar toolbar = (Toolbar)findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
```

## Toolbar 功能

1. 在manifest的activity标签中添加label可以修改Toolbar标题的文字内容

```xml
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">

        <activity
            android:label="Fruits"
            android:name=".MainActivity">

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
```

1. 在Toolbar中添加按钮图标和点击事件

  1. 在res中新建menu菜单文件夹，创建toolbar.xml文件，加入item点击图标
  2. mainactivity中 onCreateOptionsMenu动态加载菜单文件
  3. OnOptionsItemSelected中设置图标的点击事件

```xml
//menu/toolbar.xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/maodan"
        android:icon="@drawable/maodan"
        android:title="Maodan"
        app:showAsAction="always" />

    <item
        android:id="@+id/ningmeng"
        android:icon="@drawable/ningmeng"
        android:title="Ningmeng"
        app:showAsAction="ifRoom" />

    <item
        android:id="@+id/yangtao"
        android:icon="@drawable/yangtao"
        android:title="Yangtao"
        app:showAsAction="never" />
</menu>
```

```java
//Mainactivity
//动态加载toobar菜单文件
    public boolean onCreateOptionsMenu(Menu menu){
        getMenuInflater().inflate(R.menu.toolbar,menu);
        return true;
    }

    @Override
    //处理点击事件
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()){
            case R.id.maodan:
                Toast.makeText(this, "Maodan", Toast.LENGTH_SHORT).show();
                break;
            case R.id.ningmeng:
                Toast.makeText(this, "ningmeng", Toast.LENGTH_SHORT).show();
                break;
            case R.id.yangtao:
                Toast.makeText(this, "yangtao", Toast.LENGTH_SHORT).show();
                break;
            default:
        }
        return true;
    }
```
