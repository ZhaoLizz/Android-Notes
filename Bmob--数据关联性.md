# 数据关联性

- pointer：一对一、一对多 relative：多对多

- 创建数据对象

```java
public class MyUser extends BmobUser {
    private Integer age;

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}

//-------------------
//帖子
public class Post extends BmobObject {
    private String title;
    private String content;
    private MyUser author;
    private BmobFile image;
    private BmobRelation likes; //多对多关系，用于存储喜欢该帖子的用户
    getter、setter

//评论-----------------------
public class Comment extends BmobObject {
    private String content;
    private MyUser user;
    private Post post;
    getter、setter
```

## 一、一对一关系

1. 添加一对一关联

```java
//注册、登陆用户
...

//必须获取到当前用户。如果仍然用上文注册登陆时new的user添加一对一关系会出现null关联。
MyUser currentUser = BmobUser.getCurrentUser(MyUser.class);

        //创建帖子信息
        Post post = new Post();
        post.setContent("abcdefg");

        //添加一对一关联
        post.setAuthor(currentUser);
        post.save(new SaveListener<String>() {
            @Override
            public void done(String s, BmobException e) {
                if (e == null) {
                    Log.d("main", "save succeed");
                } else {
                    Log.d("main", "save failed" + e.getMessage());
                }
            }
        });
```
