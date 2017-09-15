# 隐式Intent

- 有两个标签，一个action，一个category。 如果启动其他程序的活动，要setData（添加data标签）。

- 构建好隐式intent后，启动intent，系统自动判断启动同时满足action、catagory、data标签的活动 不加自定义category时，就是默认android.intent.category.DEFAULT。

- 每个intent可以addCategory()多个catagory，只能指定一个action

**例1.隐式intent启动其他程序**

```xml
//Manifest
<activity android:name=".Main2Activity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:scheme="http"/>
            </intent-filter>
        </activity>
```

```java
public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_VIEW);
                intent.setData(Uri.parse("http://www.baidu.com"));
                startActivity(intent);
                }

public void onClick(View view) {
    Intent i = new Intent(Intent.ACTION_SEND);
    i.setType("text/plain");
    //消息内容
    i.putExtra(Intent.EXTRA_TEXT, getCrimeReport());
    //主题
    i.putExtra(Intent.EXTRA_SUBJECT, getString(R.string.crime_report_subject));

    //创建一个选择器显示相应隐式intent的全部activity
    //主要用于每次都强行打开activity选择器并修改提示标题
    i = Intent.createChooser(i, getString(R.string.send_crime_report));

    startActivity(i);
                            }

//启动联系人
//string action,Uri
        final Intent pickContact = new Intent(Intent.ACTION_PICK, ContactsContract.Contacts.CONTENT_URI);
        PackageManager packageManager = getActivity().getPackageManager();
        //找到匹配给定Intent任务的activity

        mSuspectButton = (Button) v.findViewById(R.id.btn_crime_suspect);
        //如果没有联系人应用
        if (packageManager.resolveActivity(pickContact, PackageManager.MATCH_DEFAULT_ONLY) == null) {
            mSuspectButton.setEnabled(false);
        }
        mSuspectButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                startActivityForResult(pickContact, REQUEST_CONTACT);
            }
        });
```

**例2.自定义intent** 在Manifest中

```xml
//Manifest
<activity android:name=".Main2Activity">
            <intent-filter>
                <action android:name="com.example.a6100890.note.ACTION_START"/>
                <category android:name="com.example.a6100890.note.MY_CATEGORY"/>
            </intent-filter>
        </activity>
```

```java
  Intent intent = new Intent("com.example.a6100890.note.ACTION_START");
  intent.addCategory("com.example.a6100890.note.MY_CATEGORY");
  startActivity(intent);
```

--------------------------------------------------------------------------------

## 隐式Intent的组成

- 要执行的操作：通常以Intent类的常量来表示
- 待访问数据的位置：可能是某个网页或文件的URL，或者ContentProvider中某个内容URI
- 操作涉及的数据类型：如果一个intent包含数据位置，通常可以从中推测出数据的类型
- 可选类别category：描述何时用什么方式使用某个activity

# 深入学习Intent和任务

- `startActivity(Intent)`或者`...ForResult(...)`方法默认启动匹配隐式intent的默认activity,也就是被系统默认调用了`addCategory(Intent.CATEGORY_DEFAULT)`的intent

## 解析隐式Intent

- 创建一个隐式Intent并从PackageManager那里获取到匹配它的所有activity并解析出activity的名字
- 通过`mPackageManager.queryIntentActivities(startupIntent, 0);`获取`ResolveInfo`的List集合

```java
private void setupAdapter() {
        //创建一个隐式Intent，ACTION_MAIN表示应用程序入口点
        Intent startupIntent = new Intent(Intent.ACTION_MAIN);
        //添加入口点类别
        startupIntent.addCategory(Intent.CATEGORY_LAUNCHER);

        //从PackageManager实例那里获取匹配到构建的intent的所有activity
        PackageManager pm = getActivity().getPackageManager();
        List<ResolveInfo> activities = pm.queryIntentActivities(startupIntent, 0);

        //解析ResolveInfo并排序
        Collections.sort(activities, new Comparator<ResolveInfo>() {
            @Override
            public int compare(ResolveInfo a, ResolveInfo b) {
                PackageManager pm = getActivity().getPackageManager();
                return String.CASE_INSENSITIVE_ORDER.compare(
                        //从返回的ResolveInfo中获取应用的标签名
                        a.loadLabel(pm).toString(),
                        b.loadLabel(pm).toString()
                );
            }
        });

        Log.i(TAG, "Found " + activities.size() + " activities.");
        mRecyclerView.setAdapter(new ActivityAdapter(activities));

    }
```

## 动态创建显式Intent

- 通过解析ResolveInfo动态创建显式Intent

```java
@Override
        public void onClick(View view) {
            ActivityInfo activityInfo = mResolveInfo.activityInfo;

            //作为显示Intent的一部分,发送了ACTION_MAIN操作
            Intent i = new Intent(Intent.ACTION_MAIN) 
                    //packageName,className:另一种创建显示Intent的方法:传入包名和类名
                    .setClassName(activityInfo.applicationInfo.packageName, activityInfo.name);

            startActivity(i);
        }
```
