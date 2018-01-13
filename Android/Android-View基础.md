## View的位置参数


![](http://upload-images.jianshu.io/upload_images/5734256-9da460954b65cab0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## MotionEvent
包括三种类型：
1. ACTION_DOWN
2. ACTION_MOVE
3. ACTION_UP

**getX/getY** 相对于View的坐标
**getRawX/getRawY** 相对于手机屏幕的坐标

## TouchSlop-滑动的最小距离
获取该常量（8dp）
```
ViewConfiguration.get(getContext()).getScaledTouchSlop()
```

## VelocityTracker-速度追踪
获取
```
VelocityTracker mVelocityTracker=VelocityTracker.obtain();
```
测速
```
//跟踪当前事件
mVelocityTracker.addMovement(event);
//获取速度
mVelocityTracker.computeCurrentVelocity(1000);//设置测速时间段
float xVelocity=mVelocityTracker.getXVelocity();
float yVelocity=mVelocityTracker.getYVelocity();
```
回收
```
//回收，释放内存
mVelocityTracker.clear();
mVelocityTracker.recycle();
```

## GestureDetector-手势检测
```
mGestureDetector=new GestureDetector(context, this);//实现OnGestureListener接口
mGestureDetector.setIsLongpressEnabled(false);//解决长按屏幕无法拖动的现象
mGestureDetector.setOnDoubleTapListener(this);//实现OnDoubleTapListener
```
接管onTouchEvent方法
```
boolean consume=mGestureDetector.onTouchEvent(event);
return consume;
```
**Tip:** 监听滑动相关，自己在onTouchEvent中实现；监听双击之类，用GestureDetector

## Scroller-弹性滑动
```
public void smoothScrollTo(int destX,int destY,int duration){
        mScroller.startScroll(getScrollX(),getScrollY(),destX-getScrollX(),destY-getScrollY(),duration);
    }

    public void smoothScrollBy(int x,int y,int duration){
        mScroller.startScroll(getScrollX(),getScrollY(),x+getScrollX(),y+getScrollY(),duration);
    }

    @Override
    public void computeScroll() {
        if (mScroller.computeScrollOffset()) {
            scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
            postInvalidate();
        }
    }
```
