# XML drawable

## shape drawable

_创建一个圆形的按钮_

- 创建圆形drawable：在drawable中创建xml

```xml
res/drawable/button_beat_box_normal
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="oval">
    <solid
        android:color="@color/dark_blue"/>     

</shape>
```

- 修改应用的style的button的背景,应用drawable

```xml
    <style name="BeatBoxButton" parent="Base.Widget.AppCompat.Button">
        <item name="android:background">@drawable/button_beat_box_normal</item>
    </style>
```

## state list drawable

- 根据控件的状态，state list drawable可以切换指向不同的drawable

```xml
//再创建一个drawable用于按钮按下的状态
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="oval">
    <solid
        android:color="@color/red"/>

</shape>
```

- 在drawable中创建一个state list drawable

```xml
button_beat_box
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:drawable="@drawable/button_beat_box_pressed"
          android:state_pressed="true"/>

    <item android:drawable="@drawable/button_beat_box"/>

</selector>
```

- 在Theme中使用

```xml
<!--首先让所有按钮都继承常规按钮的属性，然后选择性地修改一些属性-->
    <!--这里选择覆盖了background属性-->
    <style name="BeatBoxButton" parent="Base.Widget.AppCompat.Button">
        <item name="android:background">@drawable/button_beat_box</item>
    </style>
```

## layer list drawable

- 能让两个XML drawable合二为一，改进增加drawable功能

```xml
//改进button_beat_box_pressed
增加边框深颜色效果

<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <shape xmlns:android="http://schemas.android.com/apk/res/android"
               android:shape="oval">
            <solid
                android:color="@color/red"/>

        </shape>

    </item>

    <item>
        <shape xmlns:android="http://schemas.android.com/apk/res/android"
               android:shape="oval">
            <stroke
                android:width="4dp"
                android:color="@color/dark_red"/>

        </shape>

    </item>

</layer-list>
```
