# 滑动菜单

1.简单实现

- 利用DrawerLayout布局： 有两个子布局，一个是主屏幕的内容，一个是滑动出来的菜单的内容

  ```xml
  //activcity_main.xml
  <android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:app="http://schemas.android.com/apk/res-auto"
  android:id="@+id/drawer_layout"
  android:layout_width="match_parent"
  android:layout_height="match_parent">

  <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      xmlns:tools="http://schemas.android.com/tools"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      tools:context="com.example.a6100890.materialtest.MainActivity">

      <android.support.v7.widget.Toolbar
          android:id="@+id/toolbar"
          android:layout_width="match_parent"
          android:layout_height="?attr/actionBarSize"
          android:background="?attr/colorPrimary"
          android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
          app:popupTheme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"></android.support.v7.widget.Toolbar>

  </FrameLayout>

  <TextView
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:layout_gravity="start"
      android:background="#fff"
      android:text="This is menu"
      android:textSize="30sp" />
  </android.support.v4.widget.DrawerLayout>
  ```

**二、添加导航按钮**

- HomeAsUp按钮的id永远是android.R.id.home

- 获取DrawerLayout实例

- getSupportActionBar获取ActionBar实例
- ActionBar使显示导航按钮并设置指示图片
- 在ActionBar 的 onOptionsItemSelected中设置点击事件，使DrawerLayout打开菜单栏

```java
//MainActivity
DrawerLayout mDrawerLayout = (DrawerLayout) findViewById(R.id.drawer_layout);
        //获取标题栏ActionBar实例
        ActionBar actionBar = getSupportActionBar();
        if(actionBar != null){
            //使导航按钮图标显示
            actionBar.setDisplayHomeAsUpEnabled(true);
            //设置图标（指示器）
            actionBar.setHomeAsUpIndicator(R.drawable.ic_action_name);
```

```java
//onOptionsItemSelected
case android.R.id.home:
                mDrawerLayout.openDrawer(GravityCompat.START);
            default:
```

## 二、滑动菜单的页面布局：NavigationView  
* 包含menu和headerLayout两个子布局属性，通过app： 引用
1. 添加依赖库 
      compile 'com.android.support:design:24.2.1'
      compile 'de.hdodenhof:circleimageview:2.1.0'
2. 准备menu，创建menu文件夹，用来显示NavigationView中具体的菜单项
3. 准备headerLayout，在layout文件夹中，这个是滑动开后上方的布局
4. 在DrawerLayout中加入菜单布局NavigationView
5. 处理菜单点击事件
      1. 获取NavigationView实例
      2. 设置点击事件

```xml
//  menu/mav_menu.xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <group android:checkableBehavior="single">
        <item
            android:id="@+id/nav_call"
            android:icon="@mipmap/call"
            android:title="Call"/>

        <item
            android:id="@+id/nav_luuncher"
            android:icon="@mipmap/ic_launcher"
            android:title="Launcher"/>

        <item
            android:id="@+id/nav_yuyin"
            android:title="Yuyin"
            android:icon="@mipmap/yuyin"/>
    </group>
</menu>
```

```xml
//  layout/nav_header.xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="?attr/colorPrimary"
    android:padding="10dp">

    <de.hdodenhof.circleimageview.CircleImageView
        android:id="@+id/icon_image"
        android:layout_width="70dp"
        android:layout_height="70dp"
        android:layout_centerInParent="true"
        android:src="@drawable/touxiang" />

    <TextView
        android:id="@+id/mail"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:text="499531245@qq.com"
        android:textColor="#fff"
        android:textSize="14sp" />

    <TextView
        android:id="@+id/username"
        android:layout_above="@id/mail"
        android:text="ZhaoLizz"
        android:textColor="#fff"
        android:textSize="14sp"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</RelativeLayout>
```

```java
//MainActivity
NavigationView navigationView = (NavigationView)findViewById(R.id.nav_view);

        //把call设置为默认选中
        navigationView.setCheckedItem(R.id.nav_call);
        navigationView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                switch (item.getItemId()){
                    case R.id.nav_luuncher:
                        Toast.makeText(MainActivity.this, "luuncher", Toast.LENGTH_SHORT).show();
                        break;
                    case R.id.nav_yuyin:
                        Toast.makeText(MainActivity.this, "yuyin", Toast.LENGTH_SHORT).show();
                        break;
                    default:

                }
                return true;
            }
        });

```



