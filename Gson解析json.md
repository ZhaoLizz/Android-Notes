# Gson框架解析json字符串

- `compile 'com.google.code.gson:gson:2.7'`
- json :

`{ "error_code": 0, "reason": "成功", "result": { "code": 100000, "text": "我也是安卓啊" } }`

- 内部的result{...} 在Bean中用public内部类

- 创建javaBean，getter\,setter,toString.**数据格式必须一一对应，int、String等。否则出现无法匹配的bug**

- 解析json

```java
public class MyResponse {
    private int error_code;
    private String reason;
    private Result result;

    @Override
    public String toString() {
        return "MyResponse{" +
                "error_code=" + error_code +
                ", reason='" + reason + '\'' +
                ", result=" + result +
                '}';
    }

    public int getError_code() {
        return error_code;
    }

    public void setError_code(int error_code) {
        this.error_code = error_code;
    }

    public String getReason() {
        return reason;
    }

    public void setReason(String reason) {
        this.reason = reason;
    }

    public Result getResult() {
        return result;
    }

    public void setResult(Result result) {
        this.result = result;
    }

    public class Result {
        private int code;
        private String text;

        @Override
        public String toString() {
            return "Result{" +
                    "code=" + code +
                    ", text='" + text + '\'' +
                    '}';
        }

        public int getCode() {
            return code;
        }

        public void setCode(int code) {
            this.code = code;
        }

        public String getText() {
            return text;
        }

        public void setText(String text) {
            this.text = text;
        }
    }
}
```

```java
public void parseJson(String json) {
        Gson gson = new Gson();
        MyResponse response = gson.fromJson(json, MyResponse.class);
        if (response == null) {
            Log.d("main", "parseJson: response == null");
        } else {
            textView.setText(response.toString());

            textView.setText(response.getResult().getText());
        }
    }
```

## 解析json数组

* http://image.baidu.com/data/imgs?col=美女&tag=小清新&sort=0&pn=10&rn=10&p=channel&from=1
* 把数组内的数据映射为List对象,比如此json数组内是一个{...},所以把数组内的数据映射为List<GalleryItem>,GalleryItem也是一个javaBean,映射{...}内的数据

```java
public class GsonGalleryItem {

    private String tag;
    private List<GalleryItem> imgs;

    public String getTag() {
        return tag;
    }

    public void setTag(String tag) {
        this.tag = tag;
    }

    public List<GalleryItem> getImgs() {
        return imgs;
    }

    public void setImgs(List<GalleryItem> imgs) {
        this.imgs = imgs;
    }
}

```

```java
private List<GalleryItem> parseItems(String jsonString) throws  IOException ,JSONException{
        GsonGalleryItem gsonBean;
        Gson gson = new Gson();

        gsonBean = gson.fromJson(jsonString, GsonGalleryItem.class);
        List<GalleryItem> items = gsonBean.getImgs();
        Log.d(TAG, "parseItems by Gson " + items.size());

        return items;
    }

```
