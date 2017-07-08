# ViewPager的使用

1. 活动布局文件中写入ViewPager，PagerTabStrip
2. 准备几个页卡布局
3. 创建适配器
4. 在活动中动态加载进View页卡布局并存入List （作为ViewPagerAdapter的实参）
5. 给viewPager set adapter

```xml
//activity_main.xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:layout_height="match_parent"
    tools:context="com.example.a6100890.viewpagertest.MainActivity">

    <android.support.v4.view.ViewPager
        android:layout_gravity="center"
        android:id="@+id/viewpager"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">

    </android.support.v4.view.ViewPager>


</LinearLayout>
```

```java
public class MyViewPagerAdapter extends PagerAdapter {
    private List<View> mListViews;

    public MyViewPagerAdapter(List<View> mListViews){
        this.mListViews = mListViews;
    }

    @Override
    //删除页卡
    public void destroyItem(ViewGroup container, int position, Object object) {
        container.removeView(mListViews.get(position));
    }

    @Override
    //实例化页卡
    public Object instantiateItem(ViewGroup container, int position) {
        //在container中加入view
        container.addView(mListViews.get(position));
        return mListViews.get(position);
    }

    @Override
    public int getCount() {
        return mListViews.size();
    }

    @Override
    public boolean isViewFromObject(View view, Object object) {
        return view == object;
    }
}
```

```java
public class MainActivity extends AppCompatActivity {

    private View view1,view2;
    private List<View> views;
    private ViewPager viewPager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initViewPager();
    }

    private void initViewPager(){
        viewPager = (ViewPager) findViewById(R.id.viewpager);
        views = new ArrayList<>();
        //动态加载view
        LayoutInflater inflater = getLayoutInflater();
        view1 = inflater.inflate(R.layout.pager_1, null);
        view2 = inflater.inflate(R.layout.pager_2, null);
        views.add(view1);
        views.add(view2);
        viewPager.setAdapter(new MyViewPagerAdapter(views));
    }
}
```

## 二、 设置跟随一起滑动的标题栏PagerTitleStrip

- 标题栏作为ViewPager的子布局
- 通过重写适配器的getPageTitle(int)方法来获取标题栏

- 把PagerTitleStrip作为子控件设置在PagerView中

- 在活动、适配器中分别定义一个List

  <string>  存放标题，从活动中传入适配器构造</string>

- 重写getPageTitle(int)

```xml
<android.support.v4.view.ViewPager
        android:layout_gravity="center"
        android:id="@+id/viewpager"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">

        <android.support.v4.view.PagerTitleStrip
            android:layout_gravity="top"
            android:id="@+id/pager_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>


    </android.support.v4.view.ViewPager>
```

```java
//MainActivity
protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        titles = new ArrayList<>();
        titles.add("a");
        titles.add("b");

        initViewPager();
    }
```

```java
//Adapter
public MyViewPagerAdapter(List<View> mListViews,List<String> titles){
        this.mListViews = mListViews;
        this.titles = titles;
    }

@Override
    public CharSequence getPageTitle(int position) {
        return titles.get(position);
    }
```

## 三、PagerTabStrip可交互指示器

- 用法同上
- 唯一区别：是否可点击与下划线指示条
