# SQLite数据库

## 定义schema

```java
public class CrimeDbScheme {
    //内部类的唯一用途就是定义描述数据表元素的String常量

    public static final class CrimeTable {
        //数据库 表名
        public static final String NAME = "crimes";

        public static final class Cols {
            //列名(数据表字段)
            public static final String UUID = "uuid";
            public static final String TITLE = "title";
            public static final String FATE = "date";
            public static final String SOLVED = "solved";
        }
    }
}
```

## 创建初始数据库

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

## 打开SQLiteDatabase

调用`new CrimeBaseHelper(mContext).getWritableDatabase();`方法，`CrimeBaseHelper extends SQLiteOpenHelper`执行如下工作

1. 打开路径下数据库，如果不存在就创建数据库文件
2. 如果是首次创建数据库，就调用onCreate（SQLiteDatabase）方法，然后保存最新的版本号
3. 如果已创建过数据库，首先检查版本号，如果能更新就调用onUpgrade（SQLiteDataBase,int,int）方法升级。

```java
public class CrimeLab {
    private static CrimeLab sCrimeLab;

    private List<Crime> mCrimes;
    private Context mContext;
    private SQLiteDatabase mDatabase;

    //private 使得其他类无法创建该对象
    //除非调用get方法
    private CrimeLab(Context context) {
        mCrimes = new ArrayList<>();
        mContext = context.getApplicationContext();

        //调用getWriteableDatabase执行数据库操作
        mDatabase = new CrimeBaseHelper(mContext).getWritableDatabase();
    }

    
    public static CrimeLab get(Context context) {
        if (sCrimeLab == null) {
            sCrimeLab = new CrimeLab(context);
        }
        return sCrimeLab;
    }
}


```

## 写入数据库

* 利用ContentValues辅助类，它是一个键值存储类，只能用于处理SQLite数据
* 构建`ContentValues`

```java
//CrimeLab
private static ContentValues getContentValues(Crime crime) {
        ContentValues values = new ContentValues();
        values.put(CrimeTable.Cols.UUID, crime.getId().toString());
        values.put(CrimeTable.Cols.TITLE, crime.getTitle());
        values.put(CrimeTable.Cols.DATE, crime.getDate().getTime());
        values.put(CrimeTable.Cols.SOLVED, crime.isSolved() ? 1 : 0);\

        return values;
    }
```

#### 插入数据
```java
//CrimeLab
public void addCrime(Crime crime) {
        ContentValues values = getContentValues(crime);
        
        //数据表名、失败默认值、要写入的数据
        mDatabase.insert(CrimeTable.NAME, null, values);
    }
```

#### 更新数据

```java
//更新数据  先更新ContentValues
    public void updateCrime(Crime crime) {
        String uuidString = crime.getId().toString();
        ContentValues values = getContentValues(crime);

        //表名，ContentValues，where子句，where子句的填充值
        mDatabase.update(CrimeTable.NAME, values, CrimeTable.Cols.UUID + " = ?", new String[]{uuidString});
    }
```

#### 读取数据


* 使用CursorWrapper包装cursor

```java
//该类继承了Cursor的全部方法(包装cursor)
public class CrimeCursorWrapper extends CursorWrapper {
    public CrimeCursorWrapper(Cursor cursor) {
        super(cursor);
    }

    public Crime getCrime() {
        String uuidString = getString(getColumnIndex(CrimeTable.Cols.UUID));
        String title = getString(getColumnIndex(CrimeTable.Cols.TITLE));
        long date = getLong(getColumnIndex(CrimeTable.Cols.DATE));
        int isSolved = getInt(getColumnIndex(CrimeTable.Cols.SOLVED));

        Crime crime = new Crime(UUID.fromString(uuidString));
        crime.setTitle(title);
        crime.setDate(new Date(date));
        crime.setSolved(isSolved != 0);

        return crime;
    }
}

```


* 为了方便封装一个便利的方法

```java
private CrimeCursorWrapper queryCrimes(String whereClause, String[] whereArgs) {
        Cursor cursor = mDatabase.query(
                CrimeTable.NAME,
                null,//Columns - null selects all columns
                whereClause,
                whereArgs,
                null, //group by
                null,//having
                null //orderBy
        );
        return new CrimeCursorWrapper(cursor);
    }

```


* 调用读取的方法queryCrimes(String,String)

 ```java
 public List<Crime> getCrimes() {
         List<Crime> crimes = new ArrayList<>();
         CrimeCursorWrapper cursor = queryCrimes(null, null);
         
         try {
             cursor.moveToFirst();
             while (!cursor.isAfterLast()) {
                 crimes.add(cursor.getCrime());
                 cursor.moveToNext();
             }
         }finally {
             cursor.close();
         }
 
         return crimes;
     }

    //获取指定的Crime
     public Crime getCrime(UUID id) {
        CrimeCursorWrapper cursor = queryCrimes(CrimeTable.Cols.UUID + " = ?", new String[]{id.toString()});

        try {
            if (cursor.getCount() == 0) {
                return null;
            }

            cursor.moveToFirst();
            return cursor.getCrime();
        }finally {
            cursor.close();
        }
    }
 ```



    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
        
    }
}

```

