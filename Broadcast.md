# 广播机制

- 由Intent作为载体
- onReceive方法运行在主线程,不能执行费时操作

## 一、接收广播

### 1.动态注册:

- 可以自由地对感兴趣的广播动态注册

  1. 新建广播接收器类extendsBroadcastReceiver，重载onReceive（）
  2. 在onCreate（）中构建IntentFilter，添加想监听的广播
  3. 注册接收器(接收器、intentFilter之间建立联系)
  4. 解除注册

```java
  //MainActivity
  public class MainActivity extends AppCompatActivity {

      private IntentFilter intentFilter;
      private NetworkChangeReceiver networkChangeReceiver;

      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.activity_main);
          //构建空的intentFilter过滤器，添加想要监听的系统广播
          intentFilter = new IntentFilter();
          intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
          //注册接收器，使接收器能接受intentFilter包含的action
          networkChangeReceiver= new NetworkChangeReceiver();
          registerReceiver(networkChangeReceiver,intentFilter);
      }

      @Override
      protected void onDestroy() {
          super.onDestroy();
          unregisterReceiver(networkChangeReceiver);
      }

      class NetworkChangeReceiver extends BroadcastReceiver{
          @Override
          public void onReceive(Context context, Intent intent) {
              Toast.makeText(context,"network changes",Toast.LENGTH_SHORT).show();

              //区别多个不同的广播
              switch (intent.getAction()) {
                      case Intent.ACTION_SCREEN_OFF:
                          Log.d(TAG, "onReceive: screen off");
                          break;
                      case Intent.ACTION_SCREEN_ON:
                          Log.d(TAG, "onReceive: screen on");
                  }
          }
      }
  }
```

### 2.静态注册

- 让程序在未启动的情况下接收到广播

- 创建广播接收器

- 在Manifest中注册广播接收器

  <receiver>标签，在<receiver>
  </receiver>标签内添加<intent-filter>标签，指定想要监听的广播</intent-filter></receiver>

- 如果需要权限，添加相应的权限

```java
//new->another->Broadcast Receiver，通过as自动创建的广播接收器已经被默认在Manifest注册；
public class BootCompleteReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        // TODO: This method is called when the BroadcastReceiver is receiving
        // an Intent broadcast.
        Toast.makeText(context, "Boot Complete", Toast.LENGTH_SHORT).show();


    }
}
```

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.a6100890.note">

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <receiver
            android:name=".BootCompleteReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>
        </receiver>
    </application>

</manifest>
```

## 二、**发送**自定义广播

- 自定义广播是系统全局广播，任何应用程序都可以接收到

### 1.发送标准广播

1. 创建自定义广播接收器类extends BroadcastReceiver
2. 在Manifest中静态注册（也可以用动态注册）
3. 通过构建隐式Intent将广播发送出去

```java
public class MyBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "received in MyBroadcastReceiver", Toast.LENGTH_SHORT).show();
    }
}
```

```xml
<receiver
            android:name=".MyBroadcastReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="com.example.a6100890.Note.MY_BROADCAST"/>
            </intent-filter>
</receiver>
```

```java
public void onClick(View v) {
                Intent intent = new Intent("com.example.a6100890.Note.MY_BROADCAST");
                sendBroadcast(intent);
            }
```

### 2.发送有序广播

- 注册广播时在intent-filter标签内设置优先级(优先级高的广播接收器先接受广播)(对于同一条广播)
- 用sendOrderedBroadcast(intent，null)发送
- 在广播接收器的onReceive方法里通过abortBroadcast（）截断广播

```xml
<receiver
            android:name=".MyBroadcastReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter android:priority="100">
                <action android:name="com.example.a6100890.Note.MY_BROADCAST"/>
            </intent-filter>
        </receiver>
```

```java
public void onClick(View v) {
                Intent intent = new Intent("com.example.a6100890.Note.MY_BROADCAST");
                sendOrderedBroadcast(intent,null);
            }
```

```java
public class MyBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "received in MyBroadcastReceiver", Toast.LENGTH_SHORT).show();
        abortBroadcast();
    }
}
```

### 3.发送带有自定义权限广播

1. 在manifest中声明并添加私有权限
2. 使用`sendBroadcast(Intent,String receiverPermission)`发送带有权限的`broadcast`
3. 注册接收器时`getActivity().registerReceiver(mOnShowNotification, filter, String permission, null);`

```xml
<permission
        android:name="com.example.a6100890.photogallery.PRIVATE"
        android:protectionLevel="signature"
        />


    <uses-permission android:name="com.example.a6100890.photogallery.PRIVATE"/>
    <uses-permission android:name="android.permission.INTERNET"/>
```

```java
//发送带有自定义权限的广播
            sendBroadcast(new Intent(ACTION_SHOW_NOTIFICATION), PERM_PRIVATE);
```

```java
//注册拥有私有权限的广播接收器广播接收器
       getActivity().registerReceiver(mOnShowNotification, filter, PollService.PERM_PRIVATE, null);
```

## 三、使用本地广播

- 发送的广播只能在应用程序的内部进行传递
- 通过LocalBroadcastManager来对广播进行管理
- 本地广播只能动态实现，因为发送本地广播时程序肯定已经启动了

- 创建接收器类entends BroadcastReceiver

- 获取LocalBroadcastManager实例

- 利用localBroadcastManager的方法发送广播、注册广播接收器、解除注册

```java
class LocalReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context, "received local broadcast", Toast.LENGTH_SHORT).show();
        }
    }
```

```java
//onCreate()
        //获取manager实例
        LocalBroadcastManger localBroadcastManager = LocalBroadcastManager.getInstance(this);
        Button sendLocal = (Button)findViewById(R.id.send_local);
        sendLocal.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //通过localBroadcastmanger发送本地广播
                Intent intent = new Intent("com.example.a6100890.Note.LOCAL_BROADCAST");
                localBroadcastManager.sendBroadcast(intent);
            }
        });
        IntentFilter intentFilter3 = new IntentFilter();
        intentFilter3.addAction("com.example.a6100890.Note.LOCAL_BROADCAST");
        LocalReceiver localReceiver = new LocalReceiver();
        localBroadcastManager.registerReceiver(localReceiver,intentFilter3);
```
