# 使用HTTP协议访问网络

## 一、使用系统的HttpURLConnection

- <uses-permission android:name="android.permission.INTERNET">
  </uses-permission>

- 通过url对象获取HttpURLConnection对象实例

```java
URL url = new URL("http://www.baidu.com");
HttpURLConnection connection = (HttpURLConnection)url.openConnection();
```

1. 设置请求模式GET、POST

```java
connection.setRequestMethod("GET");
```

1. 设置一些连接信息
2. 获取服务器返回的输入流

  ```java
  InputStream in = connection.getInputStream();
  ```

3. 包装InputStream、读取信息

4. 断开连接

  ```java
  connection.disconnect();
  ```

```java
private void sendRequestWithHttpURLConnection()

    {
        new Thread(new Runnable() {
            @Override
            public void run() {
                HttpURLConnection connection = null;
                BufferedReader reader = null;
                try {
                    //发送HTTP请求，目标地址是百度首页
                    URL url = new URL("https://www.baidu.com");
                    connection = (HttpURLConnection) url.openConnection();
                    connection.setRequestMethod("GET");
                    connection.setConnectTimeout(8000);
                    connection.setReadTimeout(8000);
                    //对服务器的返回流读取
                    //  BufferedReader -- InputStreamReader -- InputStream
                    InputStream in = connection.getInputStream();  
                    reader = new BufferedReader(new InputStreamReader(in));
                    StringBuilder response = new StringBuilder();
                    String line;
                    while ((line = reader.readLine()) != null) {
                        response.append(line);
                    }
                    showResponse(response.toString());
                }catch (Exception e){
                    e.printStackTrace();
                }finally{
                    //关闭读取流、断开与服务器的链接
                    if(reader != null){
                        try{
                            reader.close();
                        }catch (IOException e){
                            e.printStackTrace();
                        }
                    }
                    if(connection != null){
                        connection.disconnect();
                    }
                }
            }
        }).start();
    }

    private void showResponse(final String response){
        //由子线程切换到主线程进行UI操作
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                responseText.setText(response);
            }
        });
    }
```

## 二、开源库OkHttp

- `compile 'com.squareup.okhttp3:okhttp:3.4.1'`
- 注册网络权限

### 1.发起HttP请求

1. 创建OkHttpClient对象
2. 连缀创建Request对象
3. 创建Call对象并调用execute()发送请求获取Response对象
4. 从Response中解析出String

  ```java
  private void sendRequestWithOkHttp(){
       new Thread(new Runnable() {
           @Override
           public void run() {
               try{
                   //先创建OkHttpClient
                   OkHttpClient client = new OkHttpClient();
                   //再连缀构建Request对象
                   Request request = new Request.Builder().url("http://www.baidu.com").build();
                   //通过newCall执行request获取回复
                   Response response = client.newCall(request).execute();
                   String responseData = response.body().string();
                   showResponse(responseData);
               }catch (Exception e){
                   e.printStackTrace();
               }
           }
       }).start();
   }
  ```

### 2. 发送POST请求

1. 创建OkHttpClient对象
2. 创建RequestBody存放待提交的参数
3. 连缀.(post(request))创建Request对象
4. 创建Call对象并调用execute()发送请求获取Response对象
5. 从Response中解析出String

```java
private void sendPostRequestWithOkHttp(){
        new Thread(new Runnable() {
            @Override
            public void run() {
                try{
                    OkHttpClient client = new OkHttpClient();
                    //先构建RequestBody，再通过body构建Request
                    RequestBody requestBody = new FormBody.Builder()
                            .add("uersname","admin")
                            .add("password","123456")
                            .build();
                    Request request = new Request.Builder()
                            .url("http://www.baidu.com")
                            .post(requestBody)
                            .build();

                    Response response = client.newCall(request).execute();
                    String responseData = response.body().string();
                    showResponse(responseData);
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        }).start();
    }
```
