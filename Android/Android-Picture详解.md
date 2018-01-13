Picture是自定义view中一个实用的工具，对应Canvas中的drawPicture方法，当然除了这个外还有另外两个方法用于绘制Picture：
- Picture自身的draw(Canvas canvas)
- PictureDrawable的draw(Canvas canvas)

下面会讨论三种方式的不同之处，首先来看下Picture的用途和主要方法

## Picture简介
Picture是用来干嘛的呢？官方参考是这样介绍的
> A Picture records drawing calls (via the canvas returned by beginRecording) and can then play them back into Canvas (via draw(Canvas) or drawPicture(Picture)). For most content (e.g. text, lines, rectangles), drawing a sequence from a picture can be faster than the equivalent API calls, since the picture performs its playback without incurring any method-call overhead.

也就是说当调用Picture的beginRecording的方法后，会返回一个Canvas，在这上面绘制的各种内容会被录制下来，之后需要的时候，直接绘制Picture会比在绘制一遍各种内容快一些。通常当有些内容我们需要绘制多次时，可以先用它录制下来，之后直接把录制的Picture绘制出来就可以了

方法|说明
------|-----
Picture()|构造方法
Picture(Picture src)|构造方法，在已有Picture基础上再进行录制
public Canvas beginRecording (int width, int height)|开始录制，在返回的Canvas上进行绘制
public void endRecording ()|结束录制
public void draw (Canvas canvas)|将Picture绘制到Canvas上
public int getWidth ()|获取宽度
public int getHeight ()|获取高度

还有两个createFromStream、writeToStream在API 18已经被弃用。上面方法理解起来也容易，beginRecording和endRecording对应，开始和结束录制，在它们两个间进行绘制操作

## 三种不同绘制方法的使用和区别
录制的内容不会直接显示，只是存储起来，想要将Picture中的内容显示出来就需要手动调用=绘制方法，有3种方法：
1. 使用Picture提供的draw方法绘制
2. 使用Canvas提供的drawPicture方法绘制
3. 将Picture包装成为PictureDrawable，使用PictureDrawable的draw方法绘制

下面来分别看看三种方法的使用。
### Picture的draw方法
```
...
    private Picture mPicture=new Picture();


    public CanvasView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        mPaint=new Paint();
        mPaint.setStrokeWidth(24);
        mPaint.setStyle(Paint.Style.FILL);
        mPaint.setStrokeCap(Paint.Cap.ROUND);

        recording();
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        mPicture.draw(canvas);
    }

    private void recording() {
        Canvas canvas=mPicture.beginRecording(400, 400);
        canvas.drawPoint(0, 0, mPaint);
        mPaint.setColor(Color.RED);
        canvas.drawPoint(250, 250, mPaint);
        mPaint.setColor(Color.BLUE);
        canvas.drawPoint(500, 500, mPaint);

        mPicture.endRecording();
    }
...
```
我们分别绘制三个点(0, 0)、(250, 250)、(500, 500)，但是传入的宽高是400，那第三个点会怎样呢

![](http://upload-images.jianshu.io/upload_images/5734256-277ca7d7f7346dc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到三个点都绘制出来了，说明传入的宽和高并不是限定画布绘制的范围，而是其相对大小（等下再具体解释），而且绘制时是将Picture的左上角和Canvas坐标轴对齐绘制
例如，将canvas坐标轴移到中心
```
@Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.translate(getWidth()/2f, getHeight()/2f);
        mPicture.draw(canvas);
}
```

![](http://upload-images.jianshu.io/upload_images/5734256-95816c004477db6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面得出，Picture的draw方法是将Picture不做任何变化原样绘制到画布上

### Canvas的drawPicture方法
这个方法有三种重载，一个参数的drawPicture(Picture picture)和Picture的draw方法效果相同，还有两个重载是通过RectF和Rect对Picture绘制进行一些控制
```
@Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawPicture(mPicture, new RectF(0, 200, mPicture.getWidth(), 200+mPicture.getHeight()));
        canvas.drawPicture(mPicture, new RectF(0, 0,mPicture.getWidth(), mPicture.getHeight()/2));
    }
```

![](http://upload-images.jianshu.io/upload_images/5734256-b9b088a231758687.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到第一次绘制的形状未变，只不过在y轴上上移了200px，第二次将高减为一般半，绘制出的Picture在y轴方向压缩了一半，也就是rect并不是对图形进行裁剪，而是缩小或放大，和Picture本身的width和height相对应。看源码更清楚：
```
/**
     * Draw the picture, stretched to fit into the dst rectangle.
     */
    public void drawPicture(@NonNull Picture picture, @NonNull RectF dst) {
        save();
        translate(dst.left, dst.top);
        if (picture.getWidth() > 0 && picture.getHeight() > 0) {
            scale(dst.width() / picture.getWidth(), dst.height() / picture.getHeight());
        }
        drawPicture(picture);
        restore();
    }
```

### PictureDrawable的draw方法
要控制在画布上的绘制区域，就需要使用PictureDrawable，设置bound
```
        // 包装成为PictureDrawable
        PictureDrawable drawable = new PictureDrawable(mPicture);
        // 设置绘制区域
        drawable.setBounds(0,0,250,mPicture.getHeight());
        // 绘制
        drawable.draw(canvas);
```

![](http://upload-images.jianshu.io/upload_images/5734256-657cda9477915226.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到绘制区域被限定在(0, 0,250, 400)范围内，而不像上面对Picture进行缩放，这里注意是限定绘制区域，而不是对Picture进行裁剪

综上，
- 一般并不使用Picture提供的draw方法，它在比较低版本的系统上绘制后可能会影响Canvas状态
- 如果想对绘制位置和比例进行控制用Canvas提供的drawPicture方法绘制
- 如果想设置绘制区域则使用PictureDrawable的draw方法




