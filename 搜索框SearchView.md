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
