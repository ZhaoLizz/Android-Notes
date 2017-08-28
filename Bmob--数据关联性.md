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

1. 查询一对一关系

```java

//查询当前用户(user)的所有帖子(Post)
MyUser currentUser = BmobUser.getCurrentUser(MyUser.class);
        BmobQuery<Post> query = new BmobQuery<>();
        query.addWhereEqualTo("author", currentUser);//查询当前用户(user)的所有帖子(Post)
        query.order("-updatedAt");
        query.include("author");//在查询帖子信息的同时查询发布人的信息
        query.findObjects(new FindListener<Post>() {
            @Override
            public void done(List<Post> list, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: query succeed "+ list.size());
                    for (Post post :
                            list) {
                        textView.setText(post.getContent() + "\n" + post.getAuthor().get...);
                    }
                } else {
                    Log.d(TAG, "done: query failed" + e.getMessage());
                }
            }
        });
```

1. 更新一对一关联

```java
//将帖子的作者改为用户B
        Post p = new Post();

        //构造用户B，如果已知pojectId，可以setobjectId，否则需要将
        //用户B查询出来
        MyUser userB = new MyUser();
        userB.setObjectId("72961f79ae");//DaMing

        p.setAuthor(userB);
        p.update(new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: updata succeed");
                } else {
                    Log.d(TAG, "done: updata failed:"+e.getMessage());
                }
            }
        });
```

1. 删除一对一关联

```java
Post post = new Post();
        post.remove("author");
        post.update("06e7ef0c5a", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: delete succeed");
                } else {
                    Log.d(TAG, "done: delete failed" + e.getMessage());
                }
            }
        });
```

## 一对多关联

1. 添加一对多关联

```java
//和一对一关联一样
Post post = new Post();
        post.setObjectId("06e7ef0c5a");
        final Comment comment = new Comment();
        comment.setContent("hello");
        comment.setPost(post);
        comment.setUser(currentUser);
        comment.save(new SaveListener<String>() {
            @Override
            public void done(String s, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: comment succeed");
                } else {
                    Log.d(TAG, "done: comment failed"+e.getMessage());
                }
            }
        });
```

2. 查询一对多关联

```java
//查询一对多关系
        BmobQuery<Comment> query = new BmobQuery<>();
        Post post = new Post();
        post.setObjectId("06e7ef0c5a");

        query.addWhereEqualTo("post", new BmobPointer(post));//包装器
        query.include("user,post.author");
        query.findObjects(new FindListener<Comment>() {
            @Override
            public void done(List<Comment> list, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: query succeed" + list.size());
                } else {
                    Log.d(TAG, "done: query failed" + e.getMessage());
                }
            }
        });
```

## 多对多关联
* 使用`Relation`表示这种多对多关系
* Relation本质上理解为其存储的是一个对象，这个对象中存储多个指向其他记录的指针
* relation 作为中介的载体连接两种数据

1. 添加多对多关系

```java
//添加多对多关联
        Post post = new Post();
        post.setObjectId("06e7ef0c5a");
        //将当前用户添加带Post表中的likes字段值中，表明当前用户喜欢该帖子
        BmobRelation relation = new BmobRelation();
        //将当前用户添加到多对多关联中
        MyUser daming = new MyUser();
        daming.setObjectId("72961f79ae");
        //relation 添加的对象的类型必须一致
        relation.add(currentUser);
        relation.add(daming);

        //多对多关联指向post的likes字段
        post.setLikes(relation);
        post.update(new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: updata succeed");
                } else {
                    Log.d(TAG, "done: updata failed" + e.getMessage());
                }
            }
        });
```

2. 查询多对多关联

```java
//查询多对多关联  addWhereRelatedTo
        //查询喜欢该帖子的所有用户
        BmobQuery<MyUser> query = new BmobQuery<>();
        Post post = new Post();
        post.setObjectId("06e7ef0c5a");

        query.addWhereRelatedTo("likes", new BmobPointer(post));
        query.findObjects(new FindListener<MyUser>() {
            @Override
            public void done(List<MyUser> list, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: query succeed" + list.size());
                } else {
                    Log.d(TAG, "done: query failed" + e.getMessage());
                }
            }
        });
```

3. 修改多对多关系

```java
//修改多对多关系
        Post p = new Post();
        p.setObjectId("06e7ef0c5a");
        //将用户B添加到Post表中的likes字段
        BmobRelation relation = new BmobRelation();
        MyUser u = new MyUser();
        u.setObjectId("...");
        relation.add(u);

        p.setLikes(relation);
        p.update(...)
```

4. 删除多对多关系

```java
//删除多对多关系
        Post p = new Post();
        p.setObjectId("06e7ef0c5a");
        BmobRelation relation = new BmobRelation();
        relation.remove(currentUser);
        p.setLikes(relation);
        p.update(new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: delete succeed");
                } else {
                    Log.d(TAG, "done: delete failed" + e.getMessage());
                }
            }
        });
```
