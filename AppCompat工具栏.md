# 工具栏

## 使用AppCompat库

- 添加AppCompat依赖项
- 使用一种AppCompat的主题
- 确保所有activity都是AppCompatActivity的子类

## 工具栏菜单项

- 先创建menu文件

```java
//CrimeListFragment
@Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //通知FragmentManager其管理的fragment应接收onCreateOptionsMenu方法调用指令
        setHasOptionsMenu(true);
    }

@Override
//填充菜单项
    public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
        super.onCreateOptionsMenu(menu, inflater);
        inflater.inflate(R.menu.fragment_crime_list, menu);
    }

//响应点击事件
@Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.new_crime:
                Crime crime = new Crime();
                CrimeLab.get(getActivity()).addCrime(crime);
                Intent intent = CrimePagerActivity.newIntent(getActivity(), crime.getId());
                startActivity(intent);
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }
```

## 层级式导航(后退键)

- 直接在`manifest`的activity标签中设置父活动就可以了

```xml
<activity
            android:name=".CrimePagerActivity"
            android:parentActivityName=".CrimeListActivity"
            >
        </activity>
```

## 设置子标题

```java
//R.string.subtitle_format : 
    <string name="subtitle_format">%1$d crimes</string>

private void updateSubtitle() {
        CrimeLab crimeLab = CrimeLab.get(getActivity());
        int crimeCount = crimeLab.getCrimes().size();
        String subtitle = getString(R.string.subtitle_format, crimeCount);

        if (!mSubtitleVissible) {
            subtitle = null;
        }

        //因为当前activity全是AppCompatActivity类型
        AppCompatActivity activity = (AppCompatActivity) getActivity();
        activity.getSupportActionBar().setSubtitle(subtitle);
    }

```

