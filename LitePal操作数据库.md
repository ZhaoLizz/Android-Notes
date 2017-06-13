# LitePal数据库
## 一、创建和更新数据库
### 1.配置LitePal

### 2.创建数据库
1. 创建表的映射类
2. 将映射类添加到映射模型列表来声明
3. 获取数据库 LitePal.getDatabase();

```java
//映射模型类
public class Book {
    private int id;
    private String author;
    private double price;
    private int pages;
    private String name;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    public int getPages() {
        return pages;
    }

    public void setPages(int pages) {
        this.pages = pages;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```
```xml
//litepal.xml
<?xml version="1.0" encoding="utf-8"?>
<litepal>
    <dbname value="BookStore"></dbname>
    <version value="1"></version>
    <list>
        <mapping class="com.example.a6100890.note.Book"></mapping>
    </list>
</litepal>

```
```java
public void onClick(View v) {
                //获取数据库
                LitePal.getDatabase();
                Log.d("MainActivity","create database");
            }
```

### 3.更新数据**库**
* 若想在原来的表的基础上添加一列，直接更改映射类
(添加 private String press，getter，setter)
* 若想再添加一个表，新创建一个映射类，在litepal.xml中声明映射关系
* 在litepal.xml表中更改数据版本

```java
//更新version value
//添加映射模型声明
<?xml version="1.0" encoding="utf-8"?>
<litepal>
    <dbname value="BookStore"></dbname>
    <version value="2"></version>
    <list>
        <mapping class="com.example.a6100890.note.Book"></mapping>
        <mapping class="com.example.a6100890.note.Category"></mapping>
    </list>
</litepal>
```

## 二、增删改查

### 1.添加数据
1. 使模型类继承自 DataSupport类
2. 获取模型类实例，对实例set..()
3. 对实例sava()

```java
public class Book extends DataSupport{
      ...
}
```
```java
public void onClick(View v) {
                Book book = new Book();
                book.setName("xiyouji");
                book.setAuthor("wuchengen");
                book.setPages(1000);
                book.setPrice(30);
                book.setPress("Unknown");
                book.save();
                Log.d("MainActivity","add data");
            }
```

### 2.更改数据
* 利用model.updataAll(),约束条件为参数,model是模型类实例
* 如果想把更新为默认值，用model.setToDefault()，列名为参数

```java
//更新数据
public void onClick(View v) {
                Book book = new Book();
                book.setPrice(29);
                book.setPress("wenxuechubanshe");
                //后面的参数补充“？”
                book.updateAll("name = ? and author = ?","xiyouji","wuchengen");
            }
```

```java
//恢复为默认数据
Book book  = new Book();
book.setToDefault("pages");
book.uodataAll();
```

### 3.删除数据
```java
public void onClick(View v) {
                //定位哪张表，哪一行
               DataSupport.deleteAll(Book.class,"price > ?","30");
           }
```

### 4.查询数据
* findAll()方法返回一个List集合，参数传入modal.class
* 也可以用带有方法连缀的find()方法精确查询，参数同上
```java
public void onClick(View v) {
                List<Book> books = DataSupport.findAll(Book.class);
                //每一行为一个Book的对象，存放到List中
                for (Book book : books) {
                    Log.d("MainActivity", book.getName() + " " + book.getAuthor() + " " + book.getPress() + " " + book.getPrice() + " " + book.getPages());
                }
            }
```

```java
public void onClick(View v) {
               List<Book> books = 
               //每一行为一个Book的对象，存放到List中
                    DataSupport
                       .select("name", "author") //选列
                       .where("pages > ?", "500") //约束
                       .order("price desc") //排序方式 desc表示降序，不写表示升序
                       .limit(3)  //显示的行数
                       .offset(1)//偏移量
                       .find(Book.class);
           }
```
