# 数据绑定

## 启用数据绑定

```java
buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
--------
    dataBinding {
        enabled = true
    }
```

## 把一般布局改造为数据绑定布局

- <layout / 使数据绑定工具生成一个同名数据绑定类(FragmentbeatBoxBinding)
- 现在实例化视图层级结构时，不再使用LayoutInflater，而是实例化数据绑定类
- 数据绑定类的`getRoot()`方法引用整个视图，`.recyclerView`引用视图子类

```xml
//fragment_beat_box.xml
<?xml version="1.0" encoding="utf-8"?>

<layout
    xmlns:android="http://schemas.android.com/apk/res/android">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</layout>
-----------------------
//list_item_sound
<?xml version="1.0" encoding="utf-8"?>
<layout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <Button
        android:layout_width="match_parent"
        android:layout_height="120dp"
        tools:text="Sound name"/>

</layout>
```

## 实例化数据绑定类

- 子类组件不再使用findViewById方法，转而使用数据绑定获取视图

```java
//BeatBoxFragment
public class BeatBoxFragment extends Fragment {
    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        //实例化绑定类,getRoot()指绑定类绑定的整个布局
        FragmentBeatBoxBinding binding = DataBindingUtil.inflate(inflater, R.layout.fragment_beat_box, container, false);
        //获取并配置recyclerView
        binding.recyclerView.setLayoutManager(new GridLayoutManager(getActivity(), 3));
        binding.recyclerView.setAdapter(new SoundAdapter());

        return binding.getRoot();
    }

    public static BeatBoxFragment newInstance() {
        return new BeatBoxFragment();
    }

    private class SoundHolder extends RecyclerView.ViewHolder {
        private ListItemSoundBinding mBinding;

        private SoundHolder(ListItemSoundBinding binding) {
            super(binding.getRoot());
            mBinding = binding;
        }
    }

    private class SoundAdapter extends RecyclerView.Adapter<SoundHolder> {
        @Override
        public SoundHolder onCreateViewHolder(ViewGroup parent, int viewType) {
            LayoutInflater inflater = LayoutInflater.from(getActivity());
            ListItemSoundBinding binding = DataBindingUtil
                    .inflate(inflater, R.layout.list_item_sound, parent, false);
            return new SoundHolder(binding);
        }

        @Override
        public void onBindViewHolder(SoundHolder holder, int position) {

        }

        @Override
        public int getItemCount() {
            return 0;
        }

    }
}
```
