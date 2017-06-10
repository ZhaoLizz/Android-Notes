 # 利用Intent在活动间传递数据

#### 1.（有数据传递的）活动的最佳启动方式：
在被启动的活动中添加活动启动器
```java
//Main2Activity活动中：
public static void actionStart(Context context, String data1, String data2) {
        Intent intent = new Intent(context, Main2Activity.class);
        intent.putExtra("key_data1", data1);
        intent.putExtra("key_data2", data2);
        context.startActivity(intent);
    }
```
```java
//由MainActivity向被启动的活动中传递数据
public void onClick(View v) {
                String data1 = "dddd1111";
                String data2 = "dddd2222";
                Main2Activity.actionStart(MainActivity.this,data1,data2);
            }
```
```java
//Activity2获取数据
        Intent intent = getIntent();
        String data1 = intent.getStringExtra("key_data1");
        String data2 = intent.getStringExtra("key_data2");
        Log.d("Main2Activity", data1 + " " + data2);
```

#### 2.向上一个活动返回数据
* 利用startActivityForResult()方法启动的活动在被finish()后自动调用
启动者的onActivityResult()方法。
* 通过在被启动活动中构造空的（只用来传递数据）Intent来向启动活动中传递数据
* 重载启动者的onActivityResult()方法。

```java
//MainActivity
public void onClick(View v) {
                Intent intent = new Intent(MainActivity.this,Main2Activity.class);
                startActivityForResult(intent,1);  //1是requestCode，在onActivityResult中用到
            }

```
```java
//Activity2(被启动的活动)
public void onClick(View v) {
                Intent intent = new Intent();  //构建空得Intent用于传递数据
                intent.putExtra("data_return","returnnnnnnnn");
                setResult(RESULT_OK,intent);  //第一个参数是resultCode，用于onActivityResult
                finish();//Activity2是由startActivityForResult()方法启动，
                    //所以finish后自动调用onActivityResult
            }
```
```java
//MainActivity
@Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        switch (requestCode) {
            case 1:
                if (resultCode == RESULT_OK) {
                    String returnedData = data.getStringExtra("data_return");
                    Log.d("MainActivity", returnedData);
                }
        }
    }
```
