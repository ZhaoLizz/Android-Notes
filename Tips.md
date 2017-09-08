# Tips

## Toast改变默认位置

- `Toast.mekeText`方法会返回Toast对象，所以不用new
- 如果直接连缀setGravity，注意该方法返回void

```java
Toast toast = Toast.makeText(MainActivity.this,"TOP",Toast.LENGTH_SHORT);
                toast.setGravity(Gravity.TOP,0,0);
                toast.show();
```

## 设备旋转及存储活动数据

- 在layout里新建一个同名的布局文件，在目录界面选择 `资源特征Oritation`, `ScreenOritation选LandScape`
- 保存数据以应对设备旋转： 通过覆盖`onSaveInstanceState(Bundle)`方法(**活动停止时调用该方法**)，将一些数据保存在bundle中,然后在onCreate(Bundle)中取回这些数据
- 最好只在bundle中保存基本类型的数据（不推荐保存实现了Serializable,Parcelable接口的对象，因为取出的对象可能已经没用了）

```java
//重写方法，存入数据
//活动中
@Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        Log.d("QuziActivity", "onSaveInstanceState: ");
        //K,V
        outState.putInt(KEY_INDEX, mCurrentIndex);
    }
```

```java
//在onCreate中取出数据
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_quiz);

        if (savedInstanceState != null) {
            //K,default values
            mCurrentIndex = savedInstanceState.getInt(KEY_INDEX, 0);
        }

    }
```

## 布局中tools：context 提供预览功能

- `tools:context`是一个命名空间，覆盖某个组件的任何属性。
- 如TextView的text属性，就可以写成`tools:text="AAAAA"`,提供预览的效果，而应用运行时不会显示该文字。

```java
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"

    //添加命名空间
    tools:context="com.example.a6100890.geoquiz.CheatActivity">


    <TextView
        android:id="@+id/text_view_answer"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="24dp"

        //利用TextView 的text属性
        tools:text="Answer"/>
</LinearLayout>
```

## fori遍历获取到所要结果时记得break

```java
for (int i = 0; i < mCrimes.size(); i++) {
            if (mCrimes.get(i).getId().equals(crimeId)) {
                mViewPager.setCurrentItem(i);
                //break!
                break;
            }
        }
```

## 菜单icon资源

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
    >

    <item
        android:id="@+id/new_crime"
        android:icon="@android:drawable/ic_menu_add"
        android:title="@string/new_crime"
        app:showAsAction="ifRoom|withText"/>

</menu>
```

## 代码控制点击返回键

```java
getActivity().onBackPressed();
```

## 获取应用上下文applicationContext

`mContext = context.getApplicationContext();`

- 只要有activity存在，android就会创建application对象，它的生命周期比任何activity都要长，常用于创建单例对象（构造方法）

## LinearLayout的divider分割线属性

```java
android:divider="?android:attr/dividerHorizontal"
android:showDividers = "middle|end|beginning|none"

```
