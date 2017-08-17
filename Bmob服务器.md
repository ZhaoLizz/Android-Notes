# Bmob服务器

1. 添加依赖库
2. 创建javaBean
3. 初始化
4. 增删改查

```java
public class Person extends BmobObject {
    private String name;
    private String address;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
```

#### 1.初始化
* String ID 在bmob主页项目里获取
`        Bmob.initialize(this,"4b250fbb6136ed10ec585ae62000aef8");
`

### 增（添加数据）
```java
Person p1 = new Person();
        p1.setName("Sam");
        p1.setAddress("USA");
        p1.save(new SaveListener<String>() {
            @Override
            public void done(String s, BmobException e) {
                if (e == null) {
                    Toast.makeText(MainActivity.this, "创建数据成功，返回的objectId为 " + s, Toast.LENGTH_SHORT).show();
                    Log.d("Main", "创建数据成功，返回的objectId为 " + s);
                } else {
                    Toast.makeText(MainActivity.this, "创建数据失败 " + e.getMessage(), Toast.LENGTH_SHORT).show();
                    Log.d("Main", "创建数据失败 " + e.getMessage());
                }
            }
        });
```

#### 查
```java
//查询获取Person表里id为86b4d64e86的数据
        BmobQuery<Person> bmobQuery = new BmobQuery<>("");
        bmobQuery.getObject("86b4d64e86", new QueryListener<Person>() {
            @Override
            public void done(Person person, BmobException e) {
                if (e == null) {
                    Log.d("Main", "查询成功" + person.getName() + " " + person.getAddress());
                } else {
                    Log.d("Main", "查询失败" + e.getMessage());
                }
            }
        });
```

#### 改

```java
//更新Person表里id为86b4d64e86的数据
        Person p2 = new Person();
        p2.setName("David");
        p2.update("86b4d64e86", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d("Main", "更新成功");
                } else {
                    Log.d("Main", "更新失败");
                }
            }
        });
```

#### 删

```java
//删除一行数据
        Person p3 = new Person();
        p3.delete("a5d016c8e3", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d("Main", "删除成功");
                } else {
                    Log.d("Main", "删除失败");
                }
            }
        });
```

