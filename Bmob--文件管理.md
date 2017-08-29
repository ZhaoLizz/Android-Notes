# Bmob文件管理 BmobFile类

- 图像文件、影像文件、音乐文件、二进制数据

--------------------------------------------------------------------------------

## 上传单一文件

```java
//上传单一文件
        String picPath = "/storage/emulated/0/Pictures/知乎/what.jpg";
        final BmobFile bmobFile = new BmobFile(new File(picPath));
        bmobFile.uploadblock(new UploadFileListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    //bmobFile.get...
                    Log.d(TAG, "done: upload succeed " + bmobFile.getFileUrl());
                } else {
                    Log.d(TAG, "done: upload failed " + e.getMessage());
                }
            }

            @Override
            public void onProgress(Integer value) {
                Log.d(TAG, "onProgress: "+String.valueOf(value)+"%");
            }
        });
```

## 批量上传文件

```java
//批量上传文件
        String picPath = "/storage/emulated/0/Pictures/知乎/what.jpg";
        String pic2Path = "/storage/emulated/0/Pictures/知乎/china.gif";
        final String[] filePaths = {picPath, pic2Path};

        BmobFile.uploadBatch(filePaths, new UploadBatchListener() {
            @Override
            //上传完成后的文件list、url地址list
            public void onSuccess(List<BmobFile> list, List<String> urls) {
                //如果数量相等则代表文件全部上传完成
                if (urls.size() == list.size()) {
                    Log.d(TAG, "onSuccess: upload succeed");
                }
            }

            @Override
            public void onProgress(int curIndex, int curPercent, int total, int totalPercent) {
                //当前第几个文件正在上传、当前上传的进度、总的文件数、总的上传进度
                Log.d(TAG, "onProgress: curindex:" + curIndex + " curPercent:" + curPercent + " total:" + total + " totalPercent: " + totalPercent);
            }

            @Override
            //错误码、错误描述
            public void onError(int i, String s) {
                Log.d(TAG, "onError: 错误码：" + i + ",错误描述：" + s);
            }
        });
```

## 下载文件

- 并不局限于通过BmobSDK上传的文件，只要提供一个文件url地址就可以调用下载方法

- 先获取BmobFile对象实例，可以通过查询数据返回的BmobFile，也可以自己构建BmobFile对象

- 调用bmobFile.download下载文件 

```java
//BmobFile的构造方法：
public BmobFile(String fileName,String group,String url){
  ...
}

//构造BmobFile对象
        BmobFile bmobFile = new BmobFile("what.jpg", "", "http://bmob-cdn-13758.b0.upaiyun.com/2017/08/29/9c5dac826b154bc7a1aaea45500e856c.jpg");
```



```java
//下载文件
        //通过查询数据得到BmobFile对象
        head = currentUser.getHead();


        //下载，有两种重载方法，可以选择写入自定义的文件，否则默认创建文件，下载到
        //context.getApplicationContext().getCacheDir()+"/bmob"
        File saveFile = new File("/storage/emulated/0/tencent/MicroMsg/WeiXin/"+head.getFilename());
        head.download(saveFile, new DownloadFileListener() {
            @Override
            public void onStart() {
                Toast.makeText(MainActivity.this, "开始下载...", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void done(String savePath, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: download succeed,savePath: " + savePath);
                } else {
                    Log.d(TAG, "done:  failed " + e.getMessage() + "error code:" + e.getErrorCode());
                }
            }

            @Override
            public void onProgress(Integer integer, long newworkSpeed) {
                Log.d(TAG, "onProgress: 下载进度：" + integer + " " + newworkSpeed);
            }
        });
```

## 删除单一文件

```java
BmobFile bmobFile = new BmobFile();
        bmobFile.setUrl("http://bmob-cdn-13758.b0.upaiyun.com/2017/08/29/9c5dac826b154bc7a1aaea45500e856c.jpg");
        bmobFile.delete(new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: delete succeed ");
                } else {
                    Log.d(TAG, "done: delete failed " + e.getMessage());
                }
            }
        });
```

## 批量删除文件

```java
//此处url必须是文件上传成功后通过bmobFile.getUrl()方法获取的
        String[] urls = new String[]{"http://bmob-cdn-13758.b0.upaiyun.com/2017/08/29/a317444f62d74f9e90383f09bb894bb4.jpg"};
        BmobFile.deleteBatch(urls, new DeleteBatchListener() {
            @Override
            public void done(String[] failUrls, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: delete succeed");
                } else {
                    if (failUrls != null) {
                        Log.d(TAG, "done: 删除失败个数：" + failUrls.length + "," + e.toString());
                    } else {
                        Log.d(TAG, "done: 全部文件删除失败：" + e.getErrorCode() + " " + e.getMessage());
                    }
                } 
            }
        });
```
