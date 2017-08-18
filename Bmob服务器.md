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

## 1.初始化

- String ID 在bmob主页项目里获取 `Bmob.initialize(this,"4b250fbb6136ed10ec585ae62000aef8");`

## 2\. 增删改查

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

//原子计数器  只能对应于Integer类型的数据
//计数器递增1  参数：javaBean的int变量名
        p2.increment("friends");
        //递增2
        p2.increment("friends", 2);
        p2.updata(UpdataListener);
```

#### 删

```java
//删除一行数据
        Person p3 = new Person();
        //删除remove某字段的值
        p3.setObjectId("a5d016c8e3");
        p3.remove("address");
        //-------------------

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

## 3\. 批量操作增删改查

#### 批量增加 insertBatch

```java
List<BmobObject> persons = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            Person person = new Person();
            person.setName("张三" + i);
            persons.add(person);
        }

        new BmobBatch().insertBatch(persons).doBatch(new QueryListListener<BatchResult>() {
            @Override
            public void done(List<BatchResult> list, BmobException e) {
                if (e == null) {
                    Log.d("main", "done");
                    for (int i = 0; i < list.size(); i++) {
                        BatchResult result = list.get(i);
                        BmobException exception = result.getError();
                        if (exception == null) {
                            Log.d("Main", "数据" + i + "添加成功:" + result.getCreatedAt() + "," + result.getObjectId() + "," + result.getUpdatedAt());
                        } else {
                            Log.d("main", "数据" + i + "添加失败:" + exception.getMessage() + "," + exception.getErrorCode());
                        }

                    }
                } else {
                    Log.d("bomb", "失败 ：" + e.getErrorCode() + "," + e.getMessage());
                }

            }
        });
```

#### 批量更新 updateBatch
```java
//批量更新
        List<BmobObject> persons1 = new ArrayList<>();
        Person p1 = new Person();
        p1.setObjectId("270ebd8652");
        p1.setAddress("a");
        Person p2 = new Person();
        p2.setObjectId("d0e2c8635a");
        p2.setAddress("b");
        Person p3 = new Person();
        p3.setAddress("c");
        p3.setObjectId("7dfb310285");
        persons1.add(p1);
        persons1.add(p2);
        persons1.add(p3);

        new BmobBatch().updateBatch(persons1).doBatch(new QueryListListener<BatchResult>() {
            @Override
            public void done(List<BatchResult> list, BmobException e) {
                if (e == null) {
                    for (int i = 0; i < list.size(); i++) {
                        BatchResult result = list.get(i);
                        BmobException exception = result.getError();
                        if (exception == null) {
                            Log.d("main", "第" + i + "个数据更新成功");
                        } else {
                            Log.d("main", "第" + i + "个数据更新失败");

                        }
                    }
                } else {
                    Log.d("bmob", "失败");
                }
            }
        });
```

#### 批量删除 deleteBatch
```java
List<BmobObject> persons = new ArrayList<BmobObject>();
Person p1 = new Person();
p1.setObjectId("38ea274d0c");
Person p2 = new Person();
p2.setObjectId("01e29165bc");
Person p3 = new Person();
p3.setObjectId("d8226c4828");

persons.add(p1);
persons.add(p2);
persons.add(p3);

//第二种方式：v3.5.0开始提供
new BmobBatch().deleteBatch(persons).doBatch(new QueryListListener<BatchResult>() {

            @Override
            public void done(List<BatchResult> o, BmobException e) {
                if(e==null){
                    for(int i=0;i<o.size();i++){
                        BatchResult result = o.get(i);
                        BmobException ex =result.getError();
                        if(ex==null){
                            log("第"+i+"个数据批量删除成功");
                        }else{
                            log("第"+i+"个数据批量删除失败："+ex.getMessage()+","+ex.getErrorCode());
                        }
                    }
                }else{
                    Log.i("bmob","失败："+e.getMessage()+","+e.getErrorCode());
                }
            }
        });

```

#### 批量查询

```java
//批量查询
        BmobQuery<Person> query = new BmobQuery<>();
        1. query.addWhereEqualTo("name", "张三0")；

        2. String[] names = {"张三0", "张三1", "张三2"};
           query.addWhereContainedIn("name", Arrays.asList(names));

        query.addwhere...
        //默认返回数据条数限制
        query.setLimit(50);
        query.findObjects(new FindListener<Person>() {
            @Override
            public void done(List<Person> list, BmobException e) {
                if (e == null) {
                    Log.d("main", "查询成功,共" + list.size() + "条数据");
                    for (Person person : list) {
                        Log.d("main", person.getName() + " " + person.getAddress());
                    }
                } else {
                    Log.d("main", "失败" + e.getMessage() + "," + e.getErrorCode());
                }
            }
        });

```

## 3. 特殊查询

1. 日期查询

```java
//查询某天的数据
        BmobQuery<Person> query3 = new BmobQuery<>();
        List<BmobQuery<Person>> and = new ArrayList<>();
        //大于00：00：00
        BmobQuery<Person> q1 = new BmobQuery<>();
        String start = "2015-05-01 00:00:00";

        //设置data类型
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date data = null;
        try {
            data = sdf.parse(start);
        } catch (ParseException e) {
            e.printStackTrace();
        }

        //K,V
        q1.addWhereGreaterThanOrEqualTo("createdAt", new BmobDate(data));
        and.add(q1);

        //小于23:59:59
        BmobQuery<Person> q2 = new BmobQuery<>();
        String end = "2015-05-01 23:59:59";

        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date data1 = null;
        try {
            data1 = sdf1.parse(end);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        q2.addWhereLessThanOrEqualTo("createdAt", new BmobDate(data1));

        and.add(q2);
        //添加复合与查询
        query3.and(and);
```

2. 数组查询(查询表的某几列)
```java
//查询有阅读和唱歌爱好的人
BmobQuery<Person> query = new BmobQuery<Person>();
String [] hobby = {"阅读","唱歌"};
query.addWhereContainsAll("hobby", Arrays.asList(hobby));
query.findObjects(new FindListener<Person>() {

    @Override
    public void done(List<Person> object, BmobException e) {
        if(e==null){
            ...
        }else{
            ...
        }
    }

});

```

3. 排序
```java
//"-score"为降序
query.order("score");
//多个排序字段
query.oreer("score,createdAt");
```


## 4. 数组

1. 增：添加数据
```java
//Person类加上private List<String> hobbys;
          //private List<Bankcard> card;

//添加数据
        Person p = new Person();
        p.setObjectId("7a436bc5ef");

        //K，V
        p.add("hobbys", "唱歌");
        p.addAll("hobbys", Arrays.asList("游泳", "看书"));

        p.add("cards", new BankCard(12333, 000000));
        List<BankCard> cards = new ArrayList<>();
        for (int i = 0; i < 2; i++) {
            cards.add(new BankCard(i * i, i * i * i));
        }
        p.addAll("cards", cards);

        p.save(new SaveListener<String>() {
            @Override
            public void done(String s, BmobException e) {
                if (e == null) {
                    Log.d("main", "保存成功");
                } else {
                    Log.d("main", "保存失败");
                }
            }
        });

        //---------------
        //只添加不重复的数据
        p.addUnique("hobbys", "唱歌");
        p.addAllUnique("cards", cards);
```

2. 更新数组数据

```java
Person p2 = new Person();
        //将List hobbys中第一个位置的值修改为爬山
        p2.setValue("hobbys.0", "爬山");

        p2.setValue("cards.0", new BankCard(999, 000000));
        p2.setValue("cards.1.bankId",1111);

        p2.update("b9f1db7f17", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d("main", "updata succeed");
                } else {
                    Log.d("main", "updata failed");
                }
            }
        });
```

3. 删除数组数据

```java
Person p = new Person();
        p.removeAll("hobbys", Arrays.asList("爬山", "看书"));
        p.update("b9f1db7f17",new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d("main", "delete succeed");
                } else {
                    Log.d("mian", "delete failed" + e.getMessage());
                }
            }
        });
```


4. 查询数组数据

```java
BmobQuery<Person> query = new BmobQuery<>();
            String[] hobby = {"唱歌"};
        query.addWhereContainsAll("hobbys", Arrays.asList(hobby));
        Log.d("main", "done");

        query.findObjects(new FindListener<Person>() {
                @Override
                public void done(List<Person> list, BmobException e) {
                    Log.d("main", "done");
                    if (e == null) {
                        Log.d("mian", "succeed");
                        for (Person person : list) {
                            Log.d("mian", person.getName() + " ");
                        }
                    } else {
                        Log.d("main", "query failed" + e.getMessage());
                    }
                }
        });
```
