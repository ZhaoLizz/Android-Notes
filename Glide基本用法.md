# Glide用法


## 一、加载图片

* ```    compile 'com.github.bumptech.glide:glide:3.7.0'```
- `Glide.with().load().into();`
- with参数可以传入`Activity、Fragment、Context`。Activity和Fragment在被销毁时停止加载图片，Context在程序被杀掉时停止加载

```java
public void loadImage(View view) {
        Log.d("MainActivity", "loadImage");

        //加载String url图片链接
        String url = "https://b-ssl.duitang.com/uploads/item/201201/03/20120103124956_KtWQG.thumb.700_0.jpg";
        Glide.with(this).load(url).into(imageView);

        //加载文件
        File file = new File(getExternalCacheDir() + "/image.jpg");
        Glide.with(this).load(file).into(imageView);

        //加载资源
        int resource = R.drawable.image;
        Glide.with(this).load(resource).into(imageView);

        //加载二进制流
        byte[] image = getImageBytes();
        Glide.with(this).load(image).into(imageView);

        //加载Uri对象
        Uri imageUri = getImageUri();
        Glide.with(this).load(imageUri).into(imageView);
    }
    }
```

## 二、占位图

- 在图片加载过程中先显示一张临时图片，等图片加载完再替换成加载的图片
- 普通占位图利用`placeholder()`
- 异常占位图`error()`

```java
public void loadImage(View view) {
        Log.d("MainActivity", "loadImage");
        String url = "https://b-ssl.duitang.com/uploads/item/201201/03/20120103124956_KtWQG.thumb.700_0.jpg";

        Glide.with(this).load(url)
                .error(R.drawable.error)
                .placeholder(R.mipmap.loading)
                .into(imageView);
    }
```

    }
```
