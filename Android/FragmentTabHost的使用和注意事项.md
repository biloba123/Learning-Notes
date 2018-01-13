一般做tab式界面我都是用的design库下的BottomNavigationView+Fragment，自己代码控制各fragment的显示和隐藏，这样做代码要多些，如果控制不当可能会在用户重返app时出现fragment重合现象。那偷懒一些的做法可以使用FragmentTabHost+Fragment来实现，只用添加相应Tab就可以，方便很多。

先看实现的效果：

![](http://upload-images.jianshu.io/upload_images/5734256-dc77afe2ee7555ea.gif?imageMogr2/auto-orient/strip)

### 布局文件
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.lvqingyang.mall.MainActivity">

    <FrameLayout
        android:id="@+id/container"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">
    </FrameLayout>

    <!--分割线-->
    <View
        android:layout_width="match_parent"
        android:layout_height="0.5dp"
        android:background="@color/div_darker"/>

    <android.support.v4.app.FragmentTabHost
        android:id="@android:id/tabhost"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/tab_bg">
        <FrameLayout
            android:id="@android:id/tabcontent"
            android:layout_width="0dp"
            android:layout_height="0dp">
        </FrameLayout>
    </android.support.v4.app.FragmentTabHost>
</LinearLayout>
```
这里注意FragmentTabHost和它的孩子FrameLayout必须使用android内部id，虽然使用上面container的FrameLayout容纳fragment但FragmentTabHost内的FrameLayout也不能省

### 代码中初始化
```
private void initTab() {
        mTabs=new ArrayList<>();
        mTabs.add(new Tab(R.string.home, R.drawable.selector_home, HomeFragment.class));
        mTabs.add(new Tab(R.string.hot, R.drawable.selector_hot, HotFragment.class));
        mTabs.add(new Tab(R.string.classes, R.drawable.selector_classes, ClassesFragment.class));
        mTabs.add(new Tab(R.string.cart, R.drawable.selector_cart, CartFragment.class));
        mTabs.add(new Tab(R.string.personal, R.drawable.selector_person, PersonalFragment.class));

        //setup
        tabhost.setup(this, getSupportFragmentManager(), R.id.container);
        mLayoutInflater=getLayoutInflater();

        for (Tab tab : mTabs) {
            tabhost.addTab(tabhost.newTabSpec(getString(tab.getTextId()))
                    .setIndicator(buildIndicator(tab)), tab.getFragment(), null);
        }

        //remove div
        tabhost.getTabWidget().setDividerDrawable(null);
        tabhost.setCurrentTab(0);
    }

    private View buildIndicator(Tab tab) {
        View view=mLayoutInflater.inflate(R.layout.indicator, null);
        TextView tvtitle = (TextView) view.findViewById(R.id.tv_title);
        ImageView ivicon = (ImageView) view.findViewById(R.id.iv_icon);

        ivicon.setImageResource(tab.getImgId());
        tvtitle.setText(tab.getTextId());

        return view;
    }
```
- Tab是简单封装每个tab的信息，包括图标、文字、对应的fragment类。
- 接下来要调用FragmentTabHost的setup方法，传入FragmentManager和容纳fragment的FrameLayout的id，这样它才能管理这些碎片。
- addTab时,第一个参数为TabSpec（传入的tag要唯一），并为它设置indicator。
- tabhost默认会在每个tab中间显示分隔线，不想要的话用
 tabhost.getTabWidget().setDividerDrawable(null); 去掉
- 最后默认在第一个位置

### 图标及文字的selector
为了让tab选择和未选择下有不同效果，要使用到selector

icon的selector
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:state_pressed="true" android:drawable="@mipmap/home_fill"/>
    <item android:state_selected="true" android:drawable="@mipmap/home_fill"/>
    <item android:drawable="@mipmap/home"/>

</selector>
```
文字颜色的selector（注意要放在color目录下，作为color资源，直接指定给textColor属性）
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:state_pressed="true" android:color="#e16531"/>
    <item android:state_selected="true" android:color="#e16531"/>
    <item android:color="@color/sub_text_color"/>

</selector>
```
### indicator布局
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:tools="http://schemas.android.com/tools"
              android:orientation="vertical"
              android:gravity="center"
              android:paddingTop="@dimen/sub_small_spacing"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content">
    <ImageView
        android:id="@+id/iv_icon"
        android:layout_width="@dimen/indicator_icon_size"
        android:layout_height="@dimen/indicator_icon_size"
        tools:src="@mipmap/home_fill"/>
    <TextView
        android:id="@+id/tv_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="@dimen/sub_small_text"
        android:layout_marginTop="@dimen/s_small_spacing"
        android:layout_marginBottom="@dimen/s_small_spacing"
        android:textColor="@color/selector_text_color"
        tools:text="@string/app_name"/>
</LinearLayout>
```
### 重用view
上面这些代码后就能实现想要的效果，但是会发现每次选中某个fragment都会重新调用它的onCreateView方法，也就是说view不能重用，这里可以在fragment中加入几行代码缓存view，避免每次都要重建view

BaseFragment.class
```
public abstract class BaseFragment extends Fragment{

    private View mView;//保存View
    private static final String TAG = "BaseFragment";
    
    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        if (mView == null) {//为空才inflater
            mView=inflaterView(inflater, container, savedInstanceState);
        }

        ViewGroup parentView= (ViewGroup) mView.getParent();
        if (parentView != null) {//如果有parent则移除防止重复添加
            parentView.removeView(mView);
        }
        
        initView(mView);
        setListener();
        return mView;
    }

    @Override
    public void onViewCreated(View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);

        initData();
        setData();
    }

    protected abstract View inflaterView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState);

    protected abstract void initView(View view);

    protected abstract void setListener();

    protected abstract void initData();

    protected abstract void setData();

}
```
