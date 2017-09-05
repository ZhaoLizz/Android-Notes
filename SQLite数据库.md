# SQLite数据库


### 定义schema

```java
public class CrimeDbScheme {
    //内部类的唯一用途就是定义描述数据表元素的String常量
    
    public static final class CrimeTable {
        //数据库表名
        public static final String NAME = "crimes";
    }

    public static final class Cols {
        //列名(数据表字段)
        public static final String UUID = "uuid";
        public static final String TITLE = "title";
        public static final String FATE = "date";
        public static final String SOLVED = "solved";
    }
    
}
```

### 创建初始数据库

1. 确认目标数据库是否存在
2. 如果不存在，首先创建数据库，然后创建数据表并初始化数据
3. 如果存在，打开并确认CrimeDbShema是否为最新版本
4. 如果是旧版本，就升级到最新版本

```java
//创建SQLiteOpenHelper的子类
public class CrimeBaseHelper extends SQLiteOpenHelper {
    private static final int VERSION = 1;
    private static final String DATABASE_NAME = "crimeBase.db";

    public CrimeBaseHelper(Context context) {
        //数据库名，CursorFactory，版本号
        super(context,DATABASE_NAME,null,VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        
    }

    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
        
    }
}

```

