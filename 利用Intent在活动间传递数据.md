# 利用Intent在活动间传递数据

## 1.（有数据传递的）活动的最佳启动方式：

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

## 2.向上一个活动返回数据

- 利用startActivityForResult()方法启动的活动在被finish()后自动调用 启动者的onActivityResult()方法。
- 通过在被启动活动中构造空的（只用来传递数据）Intent来向启动活动中传递数据,调用setResult(int requestCode,Intent)设置返回数据
- 重载启动者的onActivityResult()方法。

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

# 使用Intent传递对象

## 1\. Serializable方式

- 序列化接口，表示将一个对象转换成可存储或可传输的状态

- 让需要传递的类implements Serializable

- 现在可以通过Intent直接传递对象

- 在被启动的活动中获取对象

```java
//Person
public class Person implements Serializable{
    private String name;
    private int age;

    getter and setter
}
```

```java
Person person = new Person();
        person.setName("ZhaoLizz");
        person.setAge(19);

        //构建显示Intent
        Intent intent = new Intent(MainActivity.this, SecondActivity.class);
        intent.putExtra("person_data", person);
        startActivity(intent);

//SecondActivity中获取对象
Person person = (Person)getIntent().getSerializableExtra("person_data");

//在Fragment中获取要先getActivity().getIntent...
```

## 2\. Parcelable

- 将一个完整的对象进行分解，分解后的每一部分都是Intent支持的数据类型
- 让需要被传递的类实现Parcelable接口
- 提供CREATOR常量，注意读取的顺序必须和写入的顺序完全相同
- 效率比Serializable方式高

```java
public class Person implements Parcelable {
    private String name;
    private int age;

    getter and setter

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel parcel, int i) {
        parcel.writeString(name);
        parcel.writeInt(age);
    }

    public static final Parcelable.Creator<Person> CREATOR =
            //调用默认构造方法
            new Parcelable.Creator<Person>() {
                @Override
                public Person createFromParcel(Parcel parcel) {
                    Person person = new Person();
                    person.name = parcel.readString();
                    person.age = parcel.readInt();
                    return person;
                }

                @Override
                public Person[] newArray(int i) {
                    return new Person[i];
                }
            };
}
```

```java
//在活动中传递对象

//MainActivity：构建显示Intent
                Intent intent = new Intent(MainActivity.this, SecondActivity.class);
                intent.putExtra("person_data", person);
                startActivity(intent);

//SecondActivity接收
Person person = (Person) getIntent().getParcelableExtra("person_data");
```
