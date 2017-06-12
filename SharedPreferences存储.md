# SharedPreferences键值对方式存储

### 一、将数据存储到SharedPerences
 1.获取SharedPreferences实例 ：

*三种方法：*
 1. Context类的SharedPreferences getSharedPreferences(String name, int mode),第一个参数指定文件名，第二个传入0。
 2. Activity的getPerferences(),传入0. 自动将当年活动的类名作为SharedPerferences文件名
 3. PreferenceManager的getDefauleSharedPreferences(Context context)

2.调用SharedPreferences对象的edit()方法获取SharedPreferences.Editor()对象

3.向Editor对象中添加数据

4.调用edit的apply()提交

```java
public void onClick(View v) {
                //获取实例
                SharedPreferences sharedPreferences = getSharedPreferences("data",MODE_PRIVATE);
                //获取编辑器
                SharedPreferences.Editor editor = sharedPreferences.edit();
                //添加数据
                editor.putString("name","Tom");
                editor.putInt("age",18);
                editor.putBoolean("married",false);
                //提交
                editor.apply();
            }
```

### 二、从SharedPreferences中读取数据

* 用SharedPreferences的get(),第一个参数Key，第二个默认值

```java
public void onClick(View v) {
                SharedPreferences sharedPreferences = getSharedPreferences("data", MODE_PRIVATE);
                String name = sharedPreferences.getString("name", "");
                int age = sharedPreferences.getInt("age", 0);
                boolean married = sharedPreferences.getBoolean("married", false);
                Log.d("MainActivity", name + " " + age + " " + married);
            }
```
