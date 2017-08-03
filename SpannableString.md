# 利用SpannableString制作多彩的TextView

- 是一种字符串类型，TextView可以直接设置为显示文本
- setSpan(Object what, int start, int end, int flags)方法需要用户输入四个参数，what表示设置的格式是什么，可以是前景色、背景色也可以是可点击的文本等等

  ```
  flag:
  Spanned.SPAN_INCLUSIVE_EXCLUSIVE 从起始下标到终了下标，包括起始下标
  Spanned.SPAN_INCLUSIVE_INCLUSIVE 从起始下标到终了下标，同时包括起始下标和终了下标
  Spanned.SPAN_EXCLUSIVE_EXCLUSIVE 从起始下标到终了下标，但都不包括起始下标和终了下标
  Spanned.SPAN_EXCLUSIVE_INCLUSIVE 从起始下标到终了下标，包括终了下标
  ```

## Object what:

- ForegroundColorSpan:设置部分文本文字颜色

```java
SpannableString spannableString = new SpannableString("设置文字前景色为深蓝色");
ForegroundColorSpan colorSpan = new ForegroundColorSpan(Color.parseColor("#0099EE"));
spannableString.setSpan(colorSpan,8,spannableString.length(), Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
textView.setText(spannableString);
```

- BackgroundColorSpan:设置文本文字背景颜色

  ```java
  SpannableString spannableString = new SpannableString("设置文字的背景色为淡绿色");
  BackgroundColorSpan colorSpan = new BackgroundColorSpan(Color.parseColor("#AC00FF30"));        spannableString.setSpan(colorSpan,8,spannableString.length(),Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
  textView2.setText(spannableString);
  ```

- RelativeSizeSpan:设置文字相对大小

```java
SpannableString spannableString = new SpannableString("万丈高楼平地起");
        RelativeSizeSpan sizeSpan1 = new RelativeSizeSpan(1.2f);
        RelativeSizeSpan sizeSpan2 = new RelativeSizeSpan(1.4f);
        RelativeSizeSpan sizeSpan3 = new RelativeSizeSpan(2.0f);

        spannableString.setSpan(sizeSpan1, 0, 1, Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        spannableString.setSpan(sizeSpan2, 1, 2, Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        spannableString.setSpan(sizeSpan3, 2, 3, Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        textView.setText(spannableString);
```

- StrikethroughSpan:设置删除线

```java
SpannableString spannableString = new SpannableString("为文字设置删除线");
        StrikethroughSpan strikethroughSpan = new StrikethroughSpan();
        spannableString.setSpan(strikethroughSpan, 5, spannableString.length(), Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        textView.setText(spannableString);
```

- UnderlineSpan：设置下划线

  ```java
  SpannableString spannableString = new SpannableString("为文字设置下滑线");
       UnderlineSpan underlineSpan = new UnderlineSpan();
       spannableString.setSpan(underlineSpan, 5, spannableString.length(), Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
       textView.setText(spannableString);
  ```

- SuperscriptSpan:把文字设置为上标(最好结合小字体RelativeSizeSpan)

- SubScriptSpan：把文字设置为下标

```java
SpannableString spannableString = new SpannableString("为文字设置上标");
        SuperscriptSpan superscriptSpan = new SuperscriptSpan();
        spannableString.setSpan(superscriptSpan, 5, spannableString.length(), Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        textView.setText(spannableString);
```

- StyleSpan：设置文字风格（粗体、斜体）

```java
SpannableString spannableString = new SpannableString("为文字设置粗体、斜体风格");
        StyleSpan styleSpan_B = new StyleSpan(Typeface.BOLD);
        StyleSpan styleSpan_I = new StyleSpan(Typeface.ITALIC);
        spannableString.setSpan(styleSpan_B, 5, 7, Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        spannableString.setSpan(styleSpan_I, 8, 10, Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        textView.setText(spannableString);
```

- ImageSpan：在文本中添加图片（表情）

```java
SpannableString spannableString = new SpannableString("在文字中添加表情(表情)");
        Drawable drawable = getResources().getDrawable(R.mipmap.ic_launcher);
        drawable.setBounds(0,0,100,100);
        ImageSpan imageSpan = new ImageSpan(drawable);
        spannableString.setSpan(imageSpan,6,8,Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        textView.setText(spannableString);
```

- 文字的点击事件和超链接

`http://www.jianshu.com/p/84067ad289d2`
