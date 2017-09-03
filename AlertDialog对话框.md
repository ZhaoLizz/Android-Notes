# AlertDialog对话框的使用

- 建议将AlertDialog封装在DialogFragment的实例中使用
- 导入AlertDialog时用support.v7.app库

1. 创建弹窗的View
2. 把DlertDialog封装进Fragment
3. 用show方法启动封装的fragment
```java
//封装AlertDialog
public class DatePickerFragment extends DialogFragment{
    @NonNull
    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
      //弹出的对话框的View
        View view = LayoutInflater.from(getActivity()).inflate(R.layout.dialog_date, null);

        return new AlertDialog.Builder(getActivity())
                .setTitle(R.string.date_picker_title)
                .setPositiveButton(android.R.string.ok, null) //onClickListener
                .setView(view)
                .create();
    }
}

```

```xml
//dialog_date 内放一个选择日期的控件
<?xml version="1.0" encoding="utf-8"?>
<DatePicker xmlns:android="http://schemas.android.com/apk/res/android"
            android:id="@+id/dialog_date_picker"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:calendarViewShown="false"
            android:orientation="vertical">

</DatePicker>
```

```java
//显示Dialog（启动fragment）
public void onClick(View view) {
                FragmentManager manager = getFragmentManager();
                DatePickerFragment dialog = new DatePickerFragment();
                //传入一个tag常量，可唯一识别FragmentManager队列中的DialogFragment
                dialog.show(manager, DIALOG_DATE);
            }
```
