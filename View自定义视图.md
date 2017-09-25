# 自定义View

- 两大绘制类

  - `Canvas`拥有所有绘制操作,其方法可决定在哪里绘制,绘制什么,比如线条.圆形.字词.矩形等
  - `Paint`类决定如何绘制,其方法可指定绘制图形的特征,例如是否填充图形,使用字体.颜色等

## 创建自定义View

- 创建视图类继承自view
- 创建两个构造方法,因为视图可以从代码或者布局文件实例化.从布局文件实例化的视图会受到一个`AttributeSet`实例,该实例包含了XML布局文件中指定的XML属性

```java
public class BoxDrawingView extends View {
    private static final String TAG = "BoxDrawingView";
    private Box mCurrentBox;
    private List<Box> mBoxen = new ArrayList<>();
    private Paint mBoxPaint;
    private Paint mBackgroundPaint;

    public BoxDrawingView(Context context) {
        this(context, null);
    }

    //从xml文件中填充视图时调用此构造方法
    public BoxDrawingView(Context context, AttributeSet attrs) {
        super(context, attrs);

        mBoxPaint = new Paint();
        mBoxPaint.setColor(0x22ff0000);

        mBackgroundPaint = new Paint();
        mBackgroundPaint.setColor(0xfff8efe0);
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        PointF current = new PointF(event.getX(), event.getY());
        String action = "";

        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                action = "ACTION_DOWN";

                mCurrentBox = new Box(current);
                mBoxen.add(mCurrentBox);

                break;
            case MotionEvent.ACTION_MOVE:
                action = "ACTION_MOVE";

                if (mCurrentBox != null) {
                    mCurrentBox.setCurrent(current);
                    //调用onDraw()
                    invalidate();
                }
                break;
            case MotionEvent.ACTION_UP:
                action = "ACTION_UP";
                mCurrentBox = null;
                break;
            case MotionEvent.ACTION_CANCEL:
                //父视图拦截了触摸事件
                action = "ACTION_CANCEL";
                mCurrentBox = null;
                break;
        }
        Log.i(TAG, "onTouchEvent: " + action + "x= " + current.x + "Y= " + current.y);

        return true;
    }

    @Override
    protected void onDraw(Canvas canvas) {
        //设置背景
        canvas.drawPaint(mBackgroundPaint);

        //绘制矩形
        for (Box box : mBoxen) {
            float left = Math.min(box.getOrigin().x, box.getCurrent().x);
            float right = Math.max(box.getOrigin().x, box.getCurrent().x);
            float top = Math.min(box.getOrigin().y, box.getCurrent().y);
            float bottom = Math.max(box.getOrigin().y, box.getCurrent().y);

            canvas.drawRect(left, top, right, bottom, mBoxPaint);
        }
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
