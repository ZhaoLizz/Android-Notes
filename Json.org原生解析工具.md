# 利用JSONObject和JSONArray解析JSON

- _<http://image.baidu.com/data/imgs?col=美女&tag=小清新&sort=0&pn=10&rn=10&p=channel&from=1>_
- 先用格式化工具格式化返回的json数据,然后确定想要获取的哪几项数据,创建一个那几项数据的javaBean模型
- `JSONObject object = new JSONObject(jsonString)`创建JSONObject对象会自动根据json数据的嵌套关系创建对象树,然后依次根据对象树解析就行

```java
public class GalleryItem {
    private String mCaption;
    private String mId;
    private String mUrl;

    @Override
    public String toString() {
        return mCaption;
    }

    getter and setter
}
```

```java
//取出每张图片的信息,生成一个个GalleryItem对象,添加到List中
    private void parseItems(List<GalleryItem> items, JSONObject jsonBody) throws IOException, JSONException {
//        从对象树中取出一个object
//        JSONObject photosJsonObject = jsonBody.getJSONObject("imgs");
        JSONArray photoJsonArray = jsonBody.getJSONArray("imgs");

        //从解析的JsonArray中依次解析每个JSONObject
        for (int i = 0; i < photoJsonArray.length(); i++) {
            JSONObject photoJsonObject = photoJsonArray.getJSONObject(i);

            GalleryItem item = new GalleryItem();
            item.setId(photoJsonObject.getString("id"));
            item.setCaption(photoJsonObject.getString("desc"));
            item.setUrl(photoJsonObject.getString("imageUrl"));

            items.add(item);
        }
    }


    //发送GET请求,解析获取的JSON为GalleryItem对象并存到List中
        public List<GalleryItem> fetchItems() {
            List<GalleryItem> items = new ArrayList<>();

            try {
                String jsonString = getUrlString("http://image.baidu.com/data/imgs?col=美女&tag=小清新&sort=0&pn=10&rn=10&p=channel&from=1");
                //解析json,自动生成与原始JSON数据对应的对象树
                JSONObject jsonBody = new JSONObject(jsonString);
                parseItems(items, jsonBody);

            } catch (IOException ioe) {
                Log.e(TAG, "Failed to fetch items", ioe);
            } catch (JSONException je) {
                Log.e(TAG, "Failed to parse JSON", je);
            }

            return items;
        }
```
