# 碎片使用方式

## 1.静态加载碎片

1. 先新建一个left_fragment.xml
2. 再新建LeftFragment类继承自Fragment,动态加载布局（第一第二步构造一个完整碎片）
3. 在主活动布局中加入碎片控件

```java
//新建LeftFragment
public class LeftFragment extends Fragment {
  @Nullable
  @Override
  //动态加载布局
  public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
      View view = inflater.inflate(R.layout.left_fragment,container,false);
      return view;
  }
}
```

```xml
//acitivity_main.xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <fragment
        android:id="@+id/left_framgent"
        android:name="com.example.a6100890.note.LeftFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />
</LinearLayout>
```

## 2.动态加载碎片

- 主要通过replaceFragment方法用**已经构造好的碎片**动态替换某**布局**

```xml

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:orientation="horizontal">

  <fragment
      android:id="@+id/left_framgent"
      android:name="com.example.a6100890.note.LeftFragment"
      android:layout_width="0dp"
      android:layout_height="match_parent"
      android:layout_weight="1" />

  //添加一个空的FrameLayout用于动态加载碎片
  <FrameLayout
      android:id="@+id/right_layout"
      android:layout_width="0dp"
      android:layout_height="match_parent"
      android:layout_weight="1">

  </FrameLayout>

</LinearLayout>
```

```java
//MainActivity中
private void replaceFragment(Fragment fragment){
        //获取FragmentManager实例
        FragmentManager fragmentManager = getSupportFragmentManager();
        //开启一个事务
        FragmentTransaction transaction = fragmentManager.beginTransaction();
        transaction.replace(R.id.right_layout,fragment);
        //在碎片中模拟返回栈
        //transaction.addToBackStack(null);
        transaction.commit();//提交事务
    }
`
```

```java
//在未获取fragment实例的情况下动态加载
FragmentManager fm = getSupportFragmentManager();
        Fragment fragment = fm.findFragmentById(R.id.fragment_container);
        //此时fragment可能是null(fragment在队列中的情况下不是null)

        if (fragment == null) {
            Log.d(TAG, "onCreate: fragment == null");
            fragment = new CrimeFragment();
            fm.beginTransaction().add(R.id.right_layout, fragment).commit();
        }
```

## 销毁当前fragment

- 可以直接控制返回键`getActivity().onBackPressed()`
- 可以从栈中弹出当前fragment

```java
Fragment currentFragment = mFragmentManager.findFragmentById(R.id.activity_crime_pager_view_pager);
                mFragmentManager.beginTransaction().remove(currentFragment).commit();
```

## 3.碎片和活动之间进行通信

1. 在活动中获取碎片的实例

  - 通过findFragmentById（）方法从**活动的布局文件**中获取碎片实例

```java
//MainActivity中
FragmentManager fm = getSupportFragmentManager();
        Fragment fragment = fm.findFragmentById(R.id.fragment_container);

        if(fragment == null){
            //如果fragment没在FragmentManager的队列中
            //就新建一个Fragment并替换活动中的控件
            fragment = new CrimeFragment();
            fm.beginTransaction().add(R.id.fragment_container, fragment).commit();
        }
```

2.在碎片中获取活动的实例

- 通过getActivity()来得到和当前碎片相关联的活动实例

```java
//RightFragment2
//Button 添加在此碎片的布局中
@Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.right_fragment2,container,false);

        final Button getMainAcitivity = (Button)view.findViewById(R.id.right_button);
        getMainAcitivity.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                MainActivity mainActivity = (MainActivity)getActivity();
                Log.d("RightFragment2",mainActivity.toString());
            }
        });
        return view;
    }
```

## 碎片和碎片通信（fragmentA与B通信）

- 传入数据(A->B)：使用fragment argument
- 返回数据(B->A)：设置接收返回数据的fragment(A)为目标fragment，调用目标fragment的onActivityResult方法

**传入数据**

```java
//传入数据  CrimeFragment->DatePickerFragment
//DatePickerFragment构建newInstance
public static DatePickerFragment newInstance(Date date) {
        Bundle args = new Bundle();
        args.putSerializable(ARG_DATE, date);

        DatePickerFragment fragment = new DatePickerFragment();
        fragment.setArguments(args);
        return fragment;
    }
---------------------------

//CrimeFragment调用DatePickerFragment.newInsatance，传入参数、启动fragment
DatePickerFragment dialog = DatePickerFragment.newInstance(mCrime.getDate());
(虚拟代码)transaction.add(..).commmit
-----------------------------

//在DatePickerFragment中获取传入的argument信息
//onCreate(Bundle)
Date date = (Date) getArguments().getSerializable(ARG_DATE);
```

**返回数据**

```java
//DatePickerFragment->CrimeFragment

启动fragmentB时，为被启动的fragmentB设置接收返回数据的targetFragment（A）和请求码
DatePickerFragment dialog = DatePickerFragment.newInstance(mCrime.getDate());
                dialog.setTargetFragment(CrimeFragment.this,REQUEST_DATE);
                transaction.add(..).commmit//启动fragment

-------------------

//被启动的DatePickerFragment：写一个sendResult方法并调用
//resultCode传入的Activity.OK
private void sendResult(int resultCode, Date date) {
        if (getTargetFragment() == null) {
            return;
        }
        //把要返回的数据存入intent
        Intent intent = new Intent();
        intent.putExtra(EXTRA_DATE, date);

        //TargetFragment是CrimeFragment
        //调用TargetFragment的onActivityResult
        getTargetFragment().onActivityResult(getTargetRequestCode(), resultCode, intent);
    }

------------------
//CrimeFragment 获取返回的数据
@Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (resultCode != Activity.RESULT_OK) {
            return;
        }

        if (requestCode == REQUEST_DATE) {
            Date date = (Date) data.getSerializableExtra(DatePickerFragment.EXTRA_DATE);
            mCrime.setDate(date);
            mDateButton.setText(mCrime.getDate().toString());
        }
    }
```

## 4\. 获取Intent中的extra信息

- 一种是直接在Fragment中`getActivity().getIntent().get...(K)`,但是会造成和当前活动的强耦合性，破坏了Fragment的封装。当前fragment由某个**特定的activity**托管着，该特定的activity又有**特定的KEY**传入Intent的extra

```java
//fragment
UUID crimeId = (UUID)getActivity().getIntent().getSerializableExtra(KEY);
```

### 更好的办法：使用fragment argument

**本质还是从Activity向fragment传递数据**

- 思路：在托管当前fragment的活动中从intent中解析出extra对象，然后通过fragment argument再传入fragment。当前托管fragment的activity是中转站
- 要附加argument bundle给fragment需要调用`Fragment.setArgument(Bundle)`，而且必须在**fragment创建后、动态加载给activity前完成**，所以最好放到`newInstance`方法中，把需要从intent中获取的数据作为参数传入
- 先**构建**一个Bundle对象作为`Fragment.setArgument(Bundle)`的参数。
- 在fragment实例中setArgument

```java
//Fragment
public static CrimeFragment newInstance(UUID crimeId) {
        Bundle args = new Bundle();
        args.putSerializable(ARG_CRIME_ID, crimeId);

        CrimeFragment fragment = new CrimeFragment();
        fragment.setArguments(args);

        return fragment;
    }
```

```java
//activity
    protected Fragment createFramgent() {
        UUID crimeId = (UUID) getIntent().getSerializableExtra(EXTRA_CRIME_ID);
        return CrimeFragment.newInstance(crimeId);
    }
```

```java
//从Bundle中获取数据
public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        UUID crimeId = (UUID) getArguments().getSerializable(ARG_CRIME_ID);
        mCrime = CrimeLab.get(getActivity()).getCrime(crimeId);
    }
```

## 5\. Fragment回调接口  （与activity通信）

- 比如：平板设备一个activity托管两个fragment，创建activity时只启动了一个fragment，为了保证解耦性，需要第一个fragment通知activity再启动第二个fragment，这时用到了第一个fragment的回调接口
- 使用情况：委托工作任务给托管activity，有了回调接口，就不用关心谁是托管者，fragment可以直接调用托管activity的方法
- 直接调用托管activity方法的途径：在fragment中定义接口，让activity实现定义的接口，然后在fragment调用在本身中声明的接口，此时执行的就是在activity中实现的接口的方法

```java
//CrimeLisFragment创建接口
private Callbacks mCallbacks;

    public interface Callbacks {
        void onCrimeSelected(Crime crime);
    }

    //fragment附加给activity时调用
    @Override
    public void onAttach(Context context) {
        super.onAttach(context);
        //context <-- (fragment/activity)--implements Callbacks
        mCallbacks = (Callbacks) context;
    }


    @Override
    public void onDetach() {
        super.onDetach();
        mCallbacks = null;
    }
```

```java
//CrimeListActivity实现接口
public class CrimeListActivity extends SingleFragmentActivity implements CrimeListFragment.Callbacks

@Override
   public void onCrimeSelected(Crime crime) {
       Log.d(TAG, "onCrimeSelected: onCrimeSelected");
       //双布局layout的第二个fragment_container
       //如果使用手机用户界面，启动新的activity
       if (findViewById(R.id.detail_fragment_container) == null) {
           //通过第二个activity的newIntent方法创建intent，Extra放入crimeId
           //第二个activity被启动时从intent中获取crimeId
           Intent intent = CrimePagerActivity.newIntent(this, crime.getId());
           startActivity(intent);
       } else {
           //如果使用平板界面，将CrimeFragment放入第二个fragment_container布局中
           Fragment newDetail = CrimeFragment.newInstance(crime.getId());

           getSupportFragmentManager()
                   .beginTransaction()
                   .replace(R.id.detail_fragment_container, newDetail)
                   .commit();
       }
   }
```


```java
//CrimeListFragment调用在CrimeListFragment中声明的接口
public boolean onOptionsItemSelected(MenuItem item)                        {
        switch (item.getItemId()) {
            case R.id.new_crime:
                Crime crime = new Crime();
                CrimeLab.get(getActivity()).addCrime(crime);
                updataUI();
                
                mCallbacks.onCrimeSelected(crime);
                
                return true;
```
