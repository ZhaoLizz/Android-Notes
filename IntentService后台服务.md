# IntentService

- Service是Context的子类,所以intentService是一个context
- 服务的intent又叫**命令**,收到第一条命令时IntentService启动,触发一个后台线程然后将命令放入一个队列.然后IntentService按顺序执行针对每一条命令依次在后台线程上调用`onHandleIntent()`方法.队列中命令执行完后IntentService销毁
- 在Manifest的`application`标签内声明:`

  <service android:name=".PollService">
  </service>

- Android有时会关闭后台应用网络连接的功能以节能,所以在后台链接网络时要用`ConnectivityManager`确认网络连接是否可用

```java
public class PollService extends IntentService {
    private static final String TAG = "PollService";

    public static Intent newIntent(Context context) {
        return new Intent(context, PollService.class);
    }

    public PollService(){
        super(TAG);
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        //如果没连接网络,return
        if (!isNetworkAvailableAndConnected()) {
            return;
        }  
    }

    private boolean isNetworkAvailableAndConnected() {
        ConnectivityManager cm = (ConnectivityManager) getSystemService(CONNECTIVITY_SERVICE);

        //如果后台服务能够使用网络,它会得到一个代表当前网络连接的NetworkInfo实例
        //然后调用NetworkInfo.isConnected()方法检查当前网络是否已连接
        boolean isNetworkAvailable = cm.getActiveNetworkInfo() != null;
        boolean isNetworkConnected = isNetworkAvailable && cm.getActiveNetworkInfo().isConnected();

        return isNetworkConnected;
    }
}
```

## 使用AlarmManager延迟运行服务

- `AlarmManager`是可以发送Intent的系统服务,可以用于延时发送`PendingIntent`
- `alarmManager.setRepeating(AlarmManager.ELAPSED_REALTIME, SystemClock.elapsedRealtime(), POLL_INTERVAL_MS, pi);`

  - AlarmManager.ELAPSED_REALTIME表示基准时间值,表明我们是以`SystemClock.elapsedRealtime()`走过的时间来确定何时启动.也就是说每经过一段指定的时间就启动定时器
  - 如果使用`ALarmManager.RTC`,基准时间就是当前时刻,也就是说到了某个固定时刻就启动定时器
  - 无论使用哪个时间基准值,如果设备处于黑屏睡眠状态,定时器不会被触发.可使用对应的`AlarmManager.ELAPSED_REALTIME_WAKEUP`强制唤醒

- 取消定时器`AlarmManager.cancel(PendingIntent)`的同时要取消`PendingIntent.cancel()`.因为一个intent只能有一个PendingIntent实例

```java
public class PollService extends IntentService {
    private static final String TAG = "PollService";
    //设置1分钟间隔
    private static final long POLL_INTERVAL_MS = TimeUnit.MINUTES.toMillis(1);

    public static Intent newIntent(Context context) {
        return new Intent(context, PollService.class);
    }

    public PollService(){
        super(TAG);
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        //如果没连接网络,return
        if (!isNetworkAvailableAndConnected()) {
            return;
        }

        //获取默认query
        String query = QueryPreference.getStoredQuery(this);
        String lastResultId = QueryPreference.getLastresultId(this);
        List<GalleryItem> items;

        if (query == null) {
            items = new FlickrFetchr().fetchRecentPhotos();
        } else {
            items = new FlickrFetchr().searchPhotos(query);
        }

        if (items.size() == 0) {
            return;
        }

        String resultUrl = items.get(0).getUrl();
        if (resultUrl.equals(lastResultId)) {
            Log.i(TAG, "onHandleIntent: Got an old result: " + resultUrl);
        } else {
            Log.i(TAG, "onHandleIntent: Got a new result: " + resultUrl);
        }

        QueryPreference.setLastResultId(this, resultUrl);

    }

    private boolean isNetworkAvailableAndConnected() {
        ConnectivityManager cm = (ConnectivityManager) getSystemService(CONNECTIVITY_SERVICE);

        //如果后台服务能够使用网络,它会得到一个代表当前网络连接的NetworkInfo实例
        //然后调用NetworkInfo.isConnected()方法检查当前网络是否已连接
        boolean isNetworkAvailable = cm.getActiveNetworkInfo() != null;
        boolean isNetworkConnected = isNetworkAvailable && cm.getActiveNetworkInfo().isConnected();

        return isNetworkConnected;
    }

    //延时启动服务
    public static void setServiceAlarm(Context context, boolean isOn) {
        //创建一个用来启动PollService的PendingIntent
        Intent i = PollService.newIntent(context);
        PendingIntent pi = PendingIntent.getService(context, 0, i, 0);

        //获取AlarmManager实例
        AlarmManager alarmManager = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);

        if (isOn) {
            //延时启动PendingIntent
            alarmManager.setRepeating(AlarmManager.ELAPSED_REALTIME, SystemClock.elapsedRealtime(), POLL_INTERVAL_MS, pi);
        } else {
            //取消定时器,同步取消PendingIntent
            alarmManager.cancel(pi);
            pi.cancel();
        }
    }

    //通过检查PendingIntent是否存在来确定定时器激活与否
    public static boolean isServiceAlarmOn(Context context) {
        Intent i = PollService.newIntent(context);
        //FLAG表示如果PendingIntent不存在返回null
        PendingIntent pi = PendingIntent.getService(context, 0, i, PendingIntent.FLAG_NO_CREATE);

        return pi != null;
    }
}
```

```java
//调用方法启动后台服务
//Fragment--onCreate
//每经过1分钟后台服务启动一次
PollService.setServiceAlarm(getActivity(), true);
```
