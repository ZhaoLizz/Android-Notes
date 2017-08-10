# 下拉刷新SwipeRefreshLayout

1. 嵌套在RecyclerView外层，CoordiantorLayout内层
2. 获取swipeRefersh实例，设置setOnRefreshListener监听器
3. swipeRefresh.setRefreshing(false)刷新事件结束

```xml
<android.support.v4.widget.SwipeRefreshLayout
            android:id="@+id/swipe_refresh"
            app:layout_behavior="@string/appbar_scrolling_view_behavior"
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <android.support.v7.widget.RecyclerView
                android:id="@+id/recycler_view"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />

        </android.support.v4.widget.SwipeRefreshLayout>
```

```java
swipeRefresh = (SwipeRefreshLayout) findViewById(R.id.swipe_refresh);
        //是否缩放效果，最低高度，最高高度
        swipeRefresh.setProgressViewOffset(false,100,200);
        swipeRefresh.setColorSchemeResources(R.color.colorPrimary);
        swipeRefresh.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                new Handler().postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(MainActivity.this, "onRefresh", Toast.LENGTH_SHORT).show();
                        adapter.setNewData(DataServer.getMultipleItemNewData());
                        mSwipeRefreshLayout.setRefreshing(false);
                    }
                }, 3000);
            }
        });
```

### 注意事项
* 转动球默认紧挨swipeRefreshLayout子布局的最上面，要注意是否被其他布局遮挡
* setRefreshing（）： true时转动球会一直转动，f时停止转动
* ps： ` new Handler().postDelayed(new Runnable{ ` 开启了子线程，此时主线程和子线程是同时进行的，子线程会延时进行。所以把setRefreshing（f）放到子线程外面会导致主线程先执行setfreshing（f），出现滑动球在子线程处理耗时逻辑时不会转动的bug

