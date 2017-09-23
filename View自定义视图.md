# 自定义View

* 两大绘制类
    * `Canvas`拥有所有绘制操作,其方法可决定在哪里绘制,绘制什么,比如线条.圆形.字词.矩形等
    * `Paint`类决定如何绘制,其方法可指定绘制图形的特征,例如是否填充图形,使用字体.颜色等

## 创建自定义View

* 创建视图类继承自view
* 创建两个构造方法,因为视图可以从代码或者布局文件实例化.从布局文件实例化的视图会受到一个`AttributeSet`实例,该实例包含了XML布局文件中指定的XML属性


```java
public class BoxDrawingView extends View {
    private static final String TAG = "BoxDrawingView";

    public BoxDrawingView(Context context) {
        this(context, null);
    }

    public BoxDrawingView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    //处理触摸事件
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        //存储点信息的容器
        PointF current = new PointF(event.getX(), event.getY());
        String action = "";

        switch (event.getAction()) {
            //放下手指
            case MotionEvent.ACTION_DOWN:
                action = "ACTION_DOWN";
                break;
            case MotionEvent.ACTION_MOVE:
                action = "ACTION_MOVE";
                break;
            //抬起手指
            case MotionEvent.ACTION_UP:
                action = "ACTION_UP";
                break;
            case MotionEvent.ACTION_CANCEL:
                action = "ACTION_CANCEL";
                break;
        }

        Log.i(TAG, action + " at x= " + current.x + ", y= " + current.y);

        return true;
    }
}

```

```xml
<?xml version="1.0" encoding="utf-8"?>
<com.example.a6100890.draganddraw.BoxDrawingView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
</com.example.a6100890.draganddraw.BoxDrawingView>

```
