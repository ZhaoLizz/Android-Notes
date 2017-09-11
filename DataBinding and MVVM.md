# 数据绑定



## 启用数据绑定

```java
buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    //和buildTypes同一等级
    dataBinding {
        enabled = true
    }
```

## 把一般布局改造为数据绑定布局

* 直接绑定了某个视图，使得视图里的子组件在生成的绑定类里被实例化。注意子布局一定要加id才能通过binding实例引用
- <layout / 使数据绑定工具编译后生成一个同名数据绑定类(FragmentbeatBoxBinding)
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
- 重点在onCreateView里

```java
//BeatBoxFragment
public class BeatBoxFragment extends Fragment {
    private BeatBox mBeatBox;

    public static BeatBoxFragment newInstance() {
        return new BeatBoxFragment();
    }

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBeatBox = new BeatBox(getActivity());
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        FragmentBeatBoxBinding binding = DataBindingUtil.inflate(inflater, R.layout.fragment_beat_box, container, false);
        //配置recyclerView
        binding.recyclerView.setLayoutManager(new GridLayoutManager(getActivity(), 3));
        binding.recyclerView.setAdapter(new SoundAdapter(new BeatBox(getActivity()).getSounds()));
        
        return binding.getRoot();
    }

    //recyclerView的item的holder
    private class SoundHolder extends RecyclerView.ViewHolder {
        private ListItemSoundBinding mBinding;

        public SoundHolder(ListItemSoundBinding binding) {
            //把item的整个布局实例传给super构造方法
            super(binding.getRoot());

            mBinding = binding;

        }
    }

    private class SoundAdapter extends RecyclerView.Adapter<SoundHolder> {
        private List<Sound> mSounds;

        public SoundAdapter(List<Sound> sounds) {
            mSounds = sounds;
        }
        
        @Override
        public SoundHolder onCreateViewHolder(ViewGroup parent, int viewType) {
            LayoutInflater inflater = LayoutInflater.from(getActivity());
            ListItemSoundBinding binding = DataBindingUtil.inflate(inflater, R.layout.list_item_sound, parent, false);
            return new SoundHolder(binding);
        }

        @Override
        public void onBindViewHolder(SoundHolder holder, int position) {

        }

        @Override
        public int getItemCount() {
            return mSounds.size();
        }
    }
}

```

--------------------------------------------------------------------------------

# MVVM

- 视图-视图模型-模型
- 引入视图模型的新对象，配合数据绑定使用，负责如何显示视图
- 从前MVC控制器对象格式化视图数据的工作转给了视图模型对象。现在使用`数据绑定`，组件关联数据就能直接在布局文件里处理了
- 控制器对象(activity或fragment)开始负责初始化布局绑定类和视图模型对象

## 创建视图模型

```java
public class SoundViewModel {
    private Sound mSound;
    private BeatBox mBeatBox;

    public SoundViewModel(BeatBox beatBox) {
        mBeatBox = beatBox;
    }

    public Sound getSound() {
        return mSound;
    }

    public void setSound(Sound sound) {
        mSound = sound;
    }

    //添加绑定方法，用于adapter接口
    public String getTitle() {
        return mSound.getName();
    }
}
```

## 绑定至视图模型（把视图模型整合到布局文件里）

1. 在布局文件里声明属性

    * 声明后就可以在绑定类里用绑定表达式使用viewModel

```xml
//list_item_sound
<?xml version="1.0" encoding="utf-8"?>
<layout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <!-- 在绑定类上定义了一个叫viewModel的属性，同时还自动包括SoundViewModel的
    getter和setter方法  -->
    <!-- 绑定的类的类型是SoundViewModel视图模型 -->

    <data>
        <variable
            name="viewModel"
            type="com.example.a6100890.beatbox.SoundViewModel"/>
    </data>

    <Button
        android:layout_width="match_parent"
        android:layout_height="120dp"
        android:text="@{viewModel.title}"
        tools:text="Sound name"/>

</layout>
```

2. 关联使用视图模型

```java
//recyclerView的item的holder
    private class SoundHolder extends RecyclerView.ViewHolder {
        private ListItemSoundBinding mBinding;

        //构造方法
        public SoundHolder(ListItemSoundBinding binding) {
            //把item的整个布局实例传给super构造方法
            super(binding.getRoot());

            mBinding = binding;
            //给绑定类对象设置视图模型层对象(即绑定到该视图中的类对象)
            mBinding.setViewModel(new SoundViewModel(mBeatBox));
        }


        //给视图模型层对象更新模型数据，用于onBindViewHolder
        public void bind(Sound sound) {
            mBinding.getViewModel().setSound(sound);
            //迫使布局立即刷新
            mBinding.executePendingBindings();
        }
    }

------------------------------------------------------------------
//调用bind方法  SoundAdapter内
        public void onBindViewHolder(SoundHolder holder, int position) {
            Sound sound = mSounds.get(position);
            holder.bind(sound);
        }
```

## 绑定数据观察


- 在视图模型层继承BaseObservable类

- 使用@Bindable注解视图模型里可绑定的属性
- 每次@Bindable可绑定的属性值改变时就自动调用`notifyChange()`方法通知绑定类，视图模型对象上所有可绑定属性已经更新。或`notifyPropertyChanged(int)`表示只有getTitle方法的值有变化



```java
public class SoundViewModel extends BaseObservable{
    private Sound mSound;
    private BeatBox mBeatBox;

    public SoundViewModel(BeatBox beatBox) {
        mBeatBox = beatBox;
    }

    public Sound getSound() {
        return mSound;
    }


    public void setSound(Sound sound) {
        mSound = sound;

        notifyChange();
        //通知ListItemSoundBingding，调用list_item_sound.xml布局里指定的Button.setText(String)方法
    }

    //添加绑定方法
    //使用可绑定注解
    @Bindable
    public String getTitle() {
        return mSound.getName();
    }
}
```
