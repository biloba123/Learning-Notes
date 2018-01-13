Android默认ToastAndroid默认Toast只是一个简单的黑框框，有时觉得太单调了，不如自己实现一套较精致，不一样的Toast。
先看下效果（动图可能有点大）：

![](http://upload-images.jianshu.io/upload_images/5734256-a2d05ae14c6c7746.gif?imageMogr2/auto-orient/strip)

前四个是不同类型的Toast，第五个是个loading框。它们两者实现方式不同，分别进行讲解

## 不一样的Toast
Toast其实并不一定要是在底部弹出的黑色小框框，它也自定义不同的样式
### 自定义显示位置
toast的显示位置可以通过 方法setGravity(int gravity, int xOffset, int yOffset)来设置，
参数1是位置有Gravity.BOTTOM，Gravity.CENTER，Gravity.CENTER_HORIZONTAL等，参数2,3是相对于x轴，y轴的偏移量，单位为pix，如果想设置为一定数量dp，可以用以下方法将dp转换为pix
```
final float scale = getContext().getResources().getDisplayMetrics().density;
int pixels = (int) (dps * scale + 0.5f);
```
或者
```
TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 65, getResources().getDisplayMetrics());
```
比如来显示一个相对于屏幕中心x偏上100pix的toast
```
Toast toast=Toast.makeText(this, "啦啦啦~",Toast.LENGTH_SHORT);
toast.setGravity(Gravity.CENTER,0,-100);
toast.show();
```


![](http://upload-images.jianshu.io/upload_images/5734256-2d080da4ec9028a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)

### 加个图标
```
Toast toast = Toast.makeText(getApplicationContext(),
                        "带图片的Toast", Toast.LENGTH_LONG);
toast.setGravity(Gravity.CENTER, 0, 0);
LinearLayout llToast = (LinearLayout) toast.getView();
ImageView ivIcon = new ImageView(getApplicationContext());
ivIcon.setImageResource(R.drawable.ic_info);
llToast.addView(ivIcon, 0);
toast.show();
```
![](http://upload-images.jianshu.io/upload_images/5734256-5b0ffc3e492b2884.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)

### 完全自定义
```
Toast toast=Toast.makeText(this, "完全不一样", Toast.LENGTH_LONG);
toast.setGravity(Gravity.CENTER,0,0);

View v= getLayoutInflater().inflate(R.layout.toast,null);
toast.setView(v);
toast.show();
```
toast.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:gravity="center"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
    android:background="@drawable/bg_toast">
    <ImageView
        android:id="@+id/iv_icon"
        android:layout_width="@dimen/toast_icon_size"
        android:layout_height="@dimen/toast_icon_size"
        android:src="@drawable/ic_info"/>
    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textColor="@color/toast_text"
        android:maxEms="12"
        android:layout_marginTop="@dimen/s_small_spacing"
        android:textSize="@dimen/sub_medium_text"/>
</LinearLayout>
```
效果就是开头动图里面前四种

## 同样式的loading
loading框的话显示时间不固定，不能用toast来实现，应为它只能显示1.5s或3s，那就用dialog来实现它，这里有一点要注意，就是背景如何做到半透明，并且大小合适
```
View view=LayoutInflater.from(context).inflate
                (R.layout.toast_loading,null);
TextView tv=view.findViewById(R.id.tv);
tv.setText(text);
AVLoadingIndicatorView avl=view.findViewById(R.id.avl);
avl.setIndicator(getIndicator(context));
avl.show();

dialog=new AlertDialog.Builder(context)
            .setView(view)
            .setCancelable(false)
            .create();
dialog.show();
```
*AVLoadingIndicatorView 是一个loadingView的开源库，有多种样式，这里随机获取一种https://github.com/81813780/AVLoadingIndicatorView*

toast_loading.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:orientation="vertical"
              android:gravity="center"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:background="@drawable/bg_toast_loading">
    <com.wang.avi.AVLoadingIndicatorView
        android:id="@+id/avl"
        app:indicatorColor="@color/toast_text"
        app:indicatorName="LineScaleIndicator"
        android:layout_width="@dimen/toast_icon_size"
        android:layout_height="@dimen/toast_icon_size"/>
    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textColor="@color/toast_text"
        android:maxEms="12"
        android:layout_marginTop="@dimen/s_small_spacing"
        android:textSize="@dimen/sub_medium_text"/>
</LinearLayout>
```
然而出来的效果

![](http://upload-images.jianshu.io/upload_images/5734256-d3cd6f91a32c86cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)

这这效果。。。背景还是纯白，宽度不是wrap_content。这里需要自己写个dialog的theme，如下
```
<style name="CustomDialog" parent="android:Theme.Dialog">
        <item name="android:windowIsTranslucent">true</item>
        <item name="android:windowBackground">@android:color/transparent</item>
    </style>
```
然后在创建AlertDialog.Builder时传进去
```
dialog=new AlertDialog.Builder(context,R.style.CustomDialog)
                .setView(view)
...
```
再看效果


![](http://upload-images.jianshu.io/upload_images/5734256-60cb818e1ce8d424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)


封装类
```
import android.content.Context;
import android.support.v7.app.AlertDialog;
import android.view.Gravity;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.ImageView;
import android.widget.TextView;
import android.widget.Toast;

import com.example.myframe.R;
import com.wang.avi.AVLoadingIndicatorView;

import java.util.Random;

/**
 * Author：LvQingYang
 * Date：2017/8/18
 * Email：biloba12345@gamil.com
 * Github：https://github.com/biloba123
 *Blog：https://biloba123.github.io/
 * Info：
 */
public class MyToast {
    private static Toast toast;
    private static AlertDialog dialog;
    //info toast
    public static void info(Context context ,String text, int duration){
        showToast(context, R.drawable.ic_info, text, duration);
    }

    public static void info(Context context ,int textId, int duration){
        showToast(context, R.drawable.ic_info,context.getString(textId),duration);
    }

    public static void info(Context context ,String text){
        showToast(context, R.drawable.ic_info, text, Toast.LENGTH_SHORT);
    }

    public static void info(Context context ,int textId){
        showToast(context, R.drawable.ic_info,context.getString(textId),Toast.LENGTH_SHORT);
    }

    //success
    public static void success(Context context ,String text, int duration){
        showToast(context, R.drawable.ic_success, text, duration);
    }

    public static void success(Context context ,int textId, int duration){
        showToast(context, R.drawable.ic_success, context.getString(textId),duration);
    }

    public static void success(Context context ,String text){
        showToast(context, R.drawable.ic_success, text, Toast.LENGTH_SHORT);
    }

    public static void success(Context context ,int textId){
        showToast(context, R.drawable.ic_success, context.getString(textId),Toast.LENGTH_SHORT);
    }

    //error
    public static void error(Context context ,String text, int duration){
        showToast(context, R.drawable.ic_error, text, duration);
    }

    public static void error(Context context ,int textId, int duration){
        showToast(context, R.drawable.ic_error, context.getString(textId),duration);
    }

    public static void error(Context context ,String text){
        showToast(context, R.drawable.ic_error, text, Toast.LENGTH_SHORT);
    }

    public static void error(Context context ,int textId){
        showToast(context, R.drawable.ic_error, context.getString(textId),Toast.LENGTH_SHORT);
    }

    //loading
    public static void loading(Context context ,String text){
        View view=LayoutInflater.from(context).inflate
                (R.layout.toast_loading,null);
        TextView tv=view.findViewById(R.id.tv);
        tv.setText(text);
        AVLoadingIndicatorView avl=view.findViewById(R.id.avl);
        avl.setIndicator(getIndicator(context));
        avl.show();

        dialog=new AlertDialog.Builder(context,R.style.CustomDialog)
                .setView(view)
                .setCancelable(false)
                .create();
        dialog.show();
    }


    public static void loading(Context context ,int textId){
        loading(context, context.getString(textId));
    }

    //warning
    public static void warning(Context context ,String text, int duration){
        showToast(context, R.drawable.ic_warning, text, duration);
    }

    public static void warning(Context context ,int textId, int duration){
        showToast(context, R.drawable.ic_warning, context.getString(textId),duration);
    }

    public static void warning(Context context ,String text){
        showToast(context, R.drawable.ic_warning, text, Toast.LENGTH_SHORT);
    }

    public static void warning(Context context ,int textId){
        showToast(context, R.drawable.ic_warning, context.getString(textId),Toast.LENGTH_SHORT);
    }

    public static void cancel(){
        if (toast != null) {
            toast.cancel();
        }
        if (dialog != null) {
            dialog.cancel();
        }
    }

    static void showToast(Context context, int iconId, String text, int duration){
        if (toast != null) {
            toast.cancel();
        }
        toast=Toast.makeText(context, text, duration);
        toast.setGravity(Gravity.CENTER,0,0);

        View v= LayoutInflater.from(context).inflate
                (R.layout.toast,null);
        ImageView ivIcon=v.findViewById(R.id.iv_icon);
        ivIcon.setImageResource(iconId);
        TextView tv=v.findViewById(R.id.tv);
        tv.setText(text);

        toast.setView(v);
        toast.show();
    }

    private static String getIndicator(Context context)
    {
        String[] arrayOfString = context.getResources().getStringArray(R.array.arr_indicator);
        int i = new Random().nextInt(arrayOfString.length);
        return arrayOfString[i];
    }
}

```
