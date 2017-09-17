# 使用HTTP协议访问网络

# _不能在UI线程操作耗时逻辑（如网络请求）,必须异步操作_

## 一、使用系统的HttpURLConnection

- `<uses-permission android:name="android.permission.INTERNET"> </uses-permission>`

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

```java
//权威指南
public class FlickrFetchr {

    //从指定URL获取原始数据并返回一个字节流数组
    public byte[] getUrlBytes(String urlSpec) throws IOException {
        //包装String urlSpec 为URL对象
        URL url = new URL(urlSpec);
        //创建一个指向要访问URL的连接对象
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();

        try {
            //由于是GET请求,所以是getInputStream,如果是POST就是getOutputStream
            //调用此方法connection连接到URL地址,得到反馈代码
            InputStream in = connection.getInputStream();
            //回应码不等于正常时
            if (connection.getResponseCode() != HttpURLConnection.HTTP_OK) {
                throw new IOException(connection.getResponseMessage() + ": with" + urlSpec);
            }


            //循环调用read方法从获取的inputStream中读取数据直到取完为止
            //把读取到的数据写入ByteArrayOutputStream数组
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int bytesRead = 0;
            byte[] buffer = new byte[1024];
            while ((bytesRead = in.read(buffer)) > 0) {
                out.write(buffer, 0, bytesRead);
            }
            out.close();
            return out.toByteArray();
        } finally {
            connection.disconnect();
        }
    }

    public String getUrlString(String urlSpec) throws  IOException {
        return new String(getUrlBytes(urlSpec));
    }
}
```

### HttpUrlConnection发送POST请求

- 代码也包括GET请求的方法

```java
public class NetUtils {
    //参数：   url、post请求参数
    public static String post(String url, String content) {
        HttpURLConnection connection = null;
        try {
            //把String包装为URL
            URL mURL = new URL(url);
            //调用URL的openConnection方法获取HttpURLConnection对象
            connection = (HttpURLConnection) mURL.openConnection();

            connection.setRequestMethod("POST");
            connection.setReadTimeout(5000);//读取超时
            connection.setConnectTimeout(10000);//连接网络超时
            connection.setDoOutput(true);//允许向服务器输出内容


            //post 请求参数
            String data = content;
            //获取一个输出流，向服务器读写数据，默认情况下系统不允许向服务器输出内容
            OutputStream out = connection.getOutputStream();
            out.write(data.getBytes());
            out.flush();
            out.close();


            //处理返回结果
            int responseCode = connection.getResponseCode();
            //200表示成功
            if (responseCode == 200) {
                InputStream is = connection.getInputStream();
                String response = getStringFromInputStream(is);
                return response;
            } else {
                throw new NetworkErrorException("resonse status is " + responseCode);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (connection != null) {
                connection.disconnect();
            }
        }
        //出现expection，if不执行的情况下return null
        return null;
    }

    public static String get(String url) {
        HttpURLConnection connection = null;
        try {
            //包装String为URL
            URL mURL = new URL(url);
            connection = (HttpURLConnection) mURL.openConnection();

            connection.setRequestMethod("GET");
            connection.setReadTimeout(5000);
            connection.setConnectTimeout(10000);

            int responseCode = connection.getResponseCode();

            if (responseCode == 200) {
                InputStream is = connection.getInputStream();
                String response = getStringFromInputStream(is);
                return response;
            } else {
                throw new NetworkErrorException("response status is " + responseCode);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (connection != null) {
                connection.disconnect();
            }
        }

        return null;
    }

    private static String getStringFromInputStream(InputStream is) throws IOException {
        //outputStream为了写到本地
        ByteArrayOutputStream os = new ByteArrayOutputStream();
        byte[] buffer = new byte[1024];
        int len = -1;
        while ((len = is.read(buffer)) != -1) {
            os.write(buffer,0,len);
        }
        is.close();
        String state = os.toString();
        return state;
    }
}
```

## 二、开源库OkHttp

- `compile 'com.squareup.okhttp3:okhttp:3.4.1'`
- 注册网络权限

### 1.发起Http请求

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

### 2\. 发送POST请求

1. 创建OkHttpClient对象
2. **创建RequestBody存放待提交的参数**
3. 连缀.(post(requestBody))创建Request对象
4. 创建Call对象并调用execute()发送请求获取Response对象
5. 从Response中解析出String

```java
//键值对请求
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

```java
//Json数据post
public static final MediaType JSON = MediaType.parse("application/json; charset=utf-8");

    String post(String url, String json) throws IOException {
        OkHttpClient client = new OkHttpClient();
        RequestBody body = RequestBody.create(JSON, json);
        Request request = new Request.Builder().url(url).post(body).build();
        Response response = client.newCall(request).execute();
        if (response.isSuccessful()) {
            return response.body().toString();
        } else {
            throw new IOException("Unexpected code " + response);
        }
    }
```
