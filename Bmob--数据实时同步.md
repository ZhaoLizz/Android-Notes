# 数据实时同步

* SDK可以实现对数据表或行进行监听，当数据发生变化时Bmob会将变化的信息告知SDK
* 监听器不支持UI线程
****

 ## 1. 开始连接
```java
//开始连接
        final BmobRealTimeData rtd = new BmobRealTimeData();
        rtd.start(new ValueEventListener() {
            @Override
            public void onConnectCompleted(Exception e) {
                Log.d(TAG, "onConnectCompleted: 连接成功 " + rtd.isConnected());
            }

            @Override
            public void onDataChange(JSONObject jsonObject) {
                Log.d(TAG, "onDataChange: " + jsonObject.optString("action") + " 数据：" + jsonObject);
            }
        });

```

## 2. 监听数据和取消监听

```java
//开始连接
        final BmobRealTimeData rtd = new BmobRealTimeData();
        rtd.start(new ValueEventListener() {
            @Override
            public void onConnectCompleted(Exception e) {
                Log.d(TAG, "onConnectCompleted: 连接成功 " + rtd.isConnected());
            }

            @Override
            public void onDataChange(JSONObject jsonObject) {
                Log.d(TAG, "onDataChange: " + jsonObject.optString("action") + " 数据：" + jsonObject);
            }
        });

        
        
        //监听数据
        if (rtd.isConnected()) {
            //监听表更新，参数：表名
            rtd.subTableUpdate("Post");
            //监听表删除
            rtd.subTableDelete("Post");
            //监听行更新(表中的某一行数据)，参数：表名、objectId
            rtd.subRowUpdate("Post","06e7ef0c5a");
            //监听行删除
            rtd.subRowDelete("Post", "06e7ef0c5a");
        }
        
        //取消监听
        rtd.unsubRowDelete(...);
```
 
