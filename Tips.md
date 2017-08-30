# Tips

## Toast改变默认位置

- `Toast.mekeText`方法会返回Toast对象，所以不用new
- 如果直接连缀setGravity，注意该方法返回void

```java
Toast toast = Toast.makeText(MainActivity.this,"TOP",Toast.LENGTH_SHORT);
                toast.setGravity(Gravity.TOP,0,0);
                toast.show();
```

## 设备旋转

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
