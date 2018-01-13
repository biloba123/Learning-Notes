Fragment是Android里面中一个非常灵巧的设计，它可以看做ui模块，由活动托管。熟练的使用它们能优雅的实现很复杂的界面，而且用它们编写UI界面，相对于用一个个Activity所消耗内存少得多，而且切换很流畅，可以参考知乎客户端。

但是，Fragment使用过程中会出现一些小问题，这里讲如何在Fragment中添加menu。我们都知道在活动中添加menu只用编写好menu，在onCreateOptionsMenu中加载，并在onOptionsItemSelected中处理点击事件就可以了，按照这个流程先试下

首先编写menu.xml
```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">
    <item android:id="@+id/item_setting"
          android:icon="@drawable/ic_settings"
          android:title="@string/setting"
        app:showAsAction="always"/>
</menu>
```
这里只添加一个设置的menu item

然后要加载menu需要toolbar或actionbar，这里分两种情况：
1. 用Activity中actionbar或toolbar，在Fragment中不添加toolbar
只需在Activity中初始化好标题栏（用toolbar时要调用setSupportActionBar），在fragment中
```
@Override
    public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
        inflater.inflate(R.menu.menu_main,menu);
        super.onCreateOptionsMenu(menu, inflater);
    }
```
2. 去除Activity中标题栏，在Fragment中添加toolbar
在fragment中初始化标题栏时要注意，fragment中没有setSupportActionBar方法，也就是在标题栏还是要交给托管其的活动来处理，标题栏最终设置到window上，代码如下
```
/**
     * Fragment中初始化Toolbar
     * @param toolbar
     * @param title 标题
     * @param isDisplayHomeAsUp 是否显示返回箭头
     */
    public void initToolbar(Toolbar toolbar, String title, boolean isDisplayHomeAsUp) {
        AppCompatActivity appCompatActivity= (AppCompatActivity) getActivity();
        appCompatActivity.setSupportActionBar(toolbar);
        ActionBar actionBar = appCompatActivity.getSupportActionBar();
        if (actionBar != null) {
            actionBar.setTitle(title);
            actionBar.setDisplayHomeAsUpEnabled(isDisplayHomeAsUp);
        }
    }
```
然后再调用onCreateOptionsMenu加载到toolbar上

运行一下，你会发现两种情况下menu都没加载出来。原来要在fragment中加载menu还需在其onCreate中加一句 setHasOptionsMenu(true)，用于让活动知道有menu要加载
```
@Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setHasOptionsMenu(true);
    }
```

现在menu加载出来了

![](http://upload-images.jianshu.io/upload_images/5734256-1662e02234040b6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)

然后重写onOptionsItemSelected处理点击事件
```
@Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.item_setting:
                SettingActivity.start(getContext());
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }
```

现在就大功告成了吗，然而运行后会发现无论怎么点击onOptionsItemSelected都没被调用。可以再android training中找到原因
[http://developer.android.com/guide/topics/ui/actionbar.html](http://developer.android.com/guide/topics/ui/actionbar.html)
>Note: If you added the menu item from a fragment, via the Fragment class's onCreateOptionsMenu callback, then the system calls the respective onOptionsItemSelected() method for that fragment when the user selects one of the fragment's items. However the activity gets a chance to handle the event first, so the system calls onOptionsItemSelected() on the activity before calling the same callback for the fragment.

也就是说在虽然在fragment中重写onOptionsItemSelected，但系统会在activity中先调用onOptionsItemSelected，而活动中默认都消耗了点击事件，fragment中因此无法处理点击。所以要先在托管碎片的活动中讲frament中处理的menu item返回false，让点击事件能穿到fragment的onOptionsItemSelected中
```
@Override
    public boolean onOptionsItemSelected(MenuItem paramMenuItem) {
         switch (paramMenuItem.getItemId()) {
            case R.id.item_setting:
                return false;
            default:
                return super.onOptionsItemSelected(paramMenuItem);
        }
    }
```

**总结下**
要在fragment添加menu需要以下步骤：
1. 在fragment的onCreate中添加一句 **setHasOptionsMenu(true)；**
2. 如果用的是活动的标题栏，只需在**onCreateOptionsMenu**中加载menu；如果用Fragment自己的toolbar需要先**用活动初始化toolbar**，然后再onCreateOptionsMenu
3. **在活动中重写onOptionsItemSelected，讲需要在fragment中处理的menu item直接返回false，再在fragment的onOptionsItemSelected中处理**

