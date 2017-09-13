# 样式与主题

## 样式Style

- 要应用style需要逐个为所有组件添加它们要用到的style
- style就是把组件某几个属性集合到一起（可以看为封装），组件调用一个样式相当于添加了这个样式包括的好几个属性

- 添加样式

  - style支持继承，一个style能**继承并覆盖**其他样式的属性，用点表示继承，等同于用parent表示继承 `<style name="Strong" parent="BeatBoxButton">`
  - 命名规则：如果是继承自己内部主题，使用主题名点继承。如果继承Android操作系统中的样式或主题要用parent属性

```xml
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <style name="BeatBoxButton">
        <item name="android:background">@color/dark_blue</item>
    </style>

    <style name="BeatBoxButton.Strong">
        <!--字体加粗-->
        <item name="android:textStyle">bold</item>
    </style>

</resources>
```

1. 使用样式

```xml
<Button
        style="@style/BeatBoxButton.Strong"
        android:layout_width="match_parent"
        android:layout_height="120dp"
        android:onClick="@{()-> viewModel.onButtonClicked()}"
        android:text="@{viewModel.title}"
        tools:text="Sound name"/>
```

# 主题theme

- Theme在Manifest的application标签中声明
- Theme本质也是style，theme属性自动应用于整个应用。 style属性仅适用于单个组件，主题属性适用于所有使用同一主题的组件

  - 系统状态栏：`colorPrimaryDark` 一般来说要比工具栏颜色深一点
  - 工具栏：`colorPrimary`
  - 其他组件`colorAccent`

```xml
<!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/red</item>
        <item name="colorPrimaryDark">@color/dark_red</item>
        <item name="colorAccent">@color/gray</item>
    </style>
```

## 覆盖主题属性

_比如想覆盖主题背景色属性_

- 找到要覆盖的主题的根父主题，查看可以覆盖的属性
- 注意要覆盖属性一般带android命名空间

```xml
<style name="AppTheme" parent="Theme.AppCompat">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/red</item>
        <item name="colorPrimaryDark">@color/dark_red</item>
        <item name="colorAccent">@color/gray</item>

        <item name="android:windowBackground">@color/soothing_blue</item>
    </style>
```

## 修改按钮属性

- 定义一个style继承常规按钮属性 `parent="Base.Widget.AppCompat.Button"`
- 然后应用到正在应用的主题上

```xml
<!--首先让所有按钮都继承常规按钮的属性，然后选择性地修改一些属性-->
    <!--这里选择覆盖了background属性-->
    <style name="BeatBoxButton" parent="Base.Widget.AppCompat.Button">
        <item name="android:background">@color/dark_blue</item>
    </style>

//应用到正在使用的主题上
<!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/red</item>
        <item name="colorPrimaryDark">@color/dark_red</item>
        <item name="colorAccent">@color/gray</item>

        <item name="android:windowBackground">@color/soothing_blue</item>

        <!--Button styles-->
        <item name="buttonStyle">@style/BeatBoxButton</item>

    </style>
```
