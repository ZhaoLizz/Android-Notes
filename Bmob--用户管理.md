# 用户管理 BmobUser类
* username（必需）
* password（必需）
* email
* emailVerified:邮箱认证状态
* mobilePhoneNumber
* mobilePhoneNumberVerified

## 扩展用户类
```java
public class MyUser extends BmobUser{
  private Boolean sex;
  private String nick;
  private Integer age;

  getter and setter;
  
}
```

## 注册
```java
//注册
        MyUser bu = new MyUser();
        bu.setUsername("XiaoBing");
        bu.setPassword("123456");
        bu.setAge(19);
        bu.setEmail("xiaobing@163.com");

        bu.signUp(new SaveListener<MyUser>() {
            @Override
            public void done(MyUser myUser, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: signUp succeed " + myUser.toString());
                } else {
                    Log.d(TAG, "done: signUp failed " + e.getMessage());
                }
            }
        });
```

## 登陆

```java
//登陆
        bu.setUsername("...");
        bu.setPassword("...");
        bu.login(new SaveListener<MyUser>() {
            @Override
            public void done(MyUser myUser, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: signUp succeed " + myUser.toString());
                } else {
                    Log.d(TAG, "done: signUp failed " + e.getMessage());
                }
            }
        });

        //或者
                BmobUser.loginByAccount("XiaoBing", "123456", new LogInListener<MyUser>() {
                    @Override
                    public void done(MyUser myUser, BmobException e) {
                        if (e == null) {
                            Log.d(TAG, "done: signUp succeed " + myUser.toString());
                        } else {
                            Log.d(TAG, "done: signUp failed " + e.getMessage());
                        }
                    }
                });
```

## 当前用户
* 使用缓存的CurrentUser
****

1. **获取当前缓存用户对象**

```java
//获取初始的用户缓存对象
        BmobUser currentBmobUser = BmobUser.getCurrentUser();

        //获取扩展用户类
        MyUser currentUser = BmobUser.getCurrentUser(MyUser.class);
        
        //获取本地缓存的用户某一列的值,key 为列名
        String username = (String) BmobUser.getObjectByKey("username");
        Integer age = (Integer) BmobUser.getObjectByKey("age");

```

2. **更新本地 缓存 的用户信息 `fetchUserInfo`**

* 使用场景： 用户登陆后在web端控制台修改用户信息的情况。
* 需要先登陆，否则会报9024错误

```java
BmobUser.fetchUserInfo(new FetchUserInfoListener<BmobUser>() {
            @Override
            public void done(BmobUser bmobUser, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: updata succeed " + bmobUser.toString());
                } else {
                    Log.d(TAG, "done: updata failed" + e.getMessage());
                }
            }
        });
```

3. **更新用户的信息**

* 需要先登录后才能更新用户信息

```java
//更新用户信息
        MyUser newUser = new MyUser();
        newUser.setAge(20);
        newUser.update(currentUser.getObjectId(), new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: updata succeed ");
                } else {
                    Log.d(TAG, "done: updata failed " + e.getMessage());
                }
            }
        });
```

4. **查询用户**
```java
//查询用户
        BmobQuery<MyUser> query = new BmobQuery<>();
        query.addWhereEqualTo("username", currentUser.getUsername());
        query.findObjects(new FindListener<MyUser>() {
            @Override
            public void done(List<MyUser> list, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: query succeed " + list.size());
                } else {
                    Log.d(TAG, "done:  failed " + e.getMessage());
                }
            }
        });

```

5. **退出登录**

```java
//退出登录
        MyUser.logOut();
        MyUser outCurrentUser = BmobUser.getCurrentUser(MyUser.class);
        if (currentUser == null) {
            Log.d(TAG, "onCreate: currentUser == null");
        } else {
            Log.d(TAG, "onCreate: currentUser != null");
        }
```

6. **密码修改**
* 修改当前用户登录密码

```java
//更新密码
        BmobUser.updateCurrentUserPassword("123456", "654321", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: updata password succeed ");
                } else {
                    Log.d(TAG, "done: updata password failed " + e.getMessage());
                }
            }
        });
```

## 邮箱
1. **邮箱登陆**
    * 直接用邮箱代替用户名即可
```java
//邮箱登陆
        BmobUser.loginByAccount("xiaobing@163.com", 654321, new LogInListener<MyUser>() {
            @Override
            public void done(MyUser myUser, BmobException e) {
                ...
            }
        })
```

2. **邮箱验证**

```java
//请求邮箱验证
        BmobUser.requestEmailVerify("499531245@qq.com", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: send succeed ");
                } else {
                    Log.d(TAG, "done: send failed " + e.getMessage());
                }
            }
        });
```

3. 利用邮箱重置密码

```java
//邮箱重置密码
        BmobUser.resetPasswordByEmail("499531245@qq.com", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: send succeed ");
                } else {
                    Log.d(TAG, "done:send failed " + e.getMessage());
                }
            }
        });
```

## 手机号

1. **手机号码登陆**
* 手机号码被验证后才可以使用
* 可以手机号+密码或手机号+短信验证码登陆
* `  BmobSMS.verifySmsCode`验证通过之后`mobilePhoneNumberVerified`属性自动变为true

```java
//手机号和密码
BmobUser.loginByAccount("手机号码", "密码", new LogInListener<MyUser>() {
            @Override
            public void done(MyUser myUser, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done:login succeed " + myUser.getUsername());
                } else {
                    Log.d(TAG, "done: login failed " + e.getMessage());
                }
            }
        });
```

```java
//手机号和短信验证码
        //先请求登陆的验证码
        BmobSMS.requestSMSCode("15525011526", "模板名称", new QueryListener<Integer>() {
            @Override
            public void done(Integer integer, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: send succeed ");
                } else {
                    Log.d(TAG, "done: send failed " + e.getMessage());
                }
            }
        });
        
        //再验证登陆
        BmobUser.loginBySMSCode("15525011526", "验证码", new LogInListener<MyUser>() {
            @Override
            public void done(MyUser myUser, BmobException e) {

            }
        });
        //或者
        BmobUser.signOrLoginByMobilePhone("手机号码", "验证码", new LogInListener<MyUser>() {
            @Override
            public void done(MyUser myUser, BmobException e) {

            }
        });
```

2. **在手机号码注册或登陆时设置用户信息**

```java
MyUser user = new MyUser();
        user.setMobilePhoneNumber("...");
        user.setAge(20);
        //set...
        user.signOrLogin("验证码", new SaveListener<MyUser>() {
            @Override
            public void done(MyUser myUser, BmobException e) {
                
            }
        });

```

3. **绑定手机号码**
```java
//先请求验证码
        BmobSMS.requestSMSCode("15525011526", "模板名称", new QueryListener<Integer>() {
            @Override
            public void done(Integer integer, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: send succeed ");
                } else {
                    Log.d(TAG, "done: send failed " + e.getMessage());
                }
            }
        });
        
        //再验证
        
        BmobSMS.verifySmsCode("15525011526", "验证码", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: verify succeed ");
                  
                } else {
                    Log.d(TAG, "done: verify failed " + e.getMessage());
                }
            }
        });
```

4. **手机号重置密码**

```java
//先请求验证码
        BmobSMS.requestSMSCode("15525011526", "模板名称", new QueryListener<Integer>() {
            @Override
            public void done(Integer integer, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: send succeed 短信id："+ integer);
                } else {
                    Log.d(TAG, "done: send failed " + e.getMessage());
                }
            }
        });

        BmobUser.resetPasswordBySMSCode("code", "password", new UpdateListener() {
            @Override
            public void done(BmobException e) {

            }
        });

```

5. **查询短信发送和验证状态**

```java
//查询短信发送和验证状态
        BmobSMS.querySmsState(int_smsId, new QueryListener<BmobSmsState>() {
            @Override
            public void done(BmobSmsState bmobSmsState, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "短信状态：" + bmobSmsState.getSmsState() + " 验证状态：" + bmobSmsState.getVerifyState());
                } else {
                    Log.d(TAG, "done:  failed " + e.getMessage());
                }
            }
        });
```

## 第三方登陆

待更新
