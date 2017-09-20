# SearchView搜索框

1. 活动布局中加入控件
2. 设置搜索监听： 包含：onQueryTextSubmit、onQueryTextChange

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.example.a6100890.searchviewtest.MainActivity">

    <SearchView
        android:iconifiedByDefault="false"
        android:id="@+id/search_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:queryHint="hint" />
    <View
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#2204ff60"/>


</LinearLayout>
```

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        SearchView searchView = (SearchView) findViewById(R.id.search_view);
        searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
            @Override
            //点击搜索时调用
            public boolean onQueryTextSubmit(String query) {
                return false;
            }

            @Override
            //文字改变时调用
            public boolean onQueryTextChange(String newText) {
                return false;
            }
        });
    }
}
```


## 使用定义在Menu中的SearchView


* `app:actionViewClass="android.support.v7.widget.SearchView"`


```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
    >

    <item
        android:id="@+id/menu_item_search"
        android:title="@string/search"
        app:actionViewClass="android.support.v7.widget.SearchView"
        app:showAsAction="ifRoom"/>

    <item
        android:id="@+id/menu_item_clear"
        android:title="@string/clear_search"
        app:showAsAction="never"/>
</menu>

```


```java
@Override
    public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
        super.onCreateOptionsMenu(menu, inflater);
        //把menu文件加载进工具栏
        inflater.inflate(R.menu.fragment_photo_gallery, menu);

                //从MenuItem中获取SearchView实例
        MenuItem searchItem = menu.findItem(R.id.menu_item_search);
        final SearchView searchView = (SearchView) searchItem.getActionView();

        searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
            @Override
            public boolean onQueryTextSubmit(String s) {
                Log.d(TAG, "onQueryTextSubmit: " + s);
                updataItems();
                return true;
            }

            @Override
            public boolean onQueryTextChange(String s) {
                Log.d(TAG, "onQueryTextChange: " + s);
                return false;
            }
        });
    }

```
