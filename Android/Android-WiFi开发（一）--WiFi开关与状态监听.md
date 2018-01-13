>之前开发了一个WiFi，热点相关的应用。因为对这方面也不熟悉，刚开始找资料看书，但看明白实现时，发现随着android版本更新，相关api有较大改动，之前的代码不能用。经过一番探索，最后实现出来了，现在把它记录下来，希望能给需要的人一些帮助。

首先来讲WiFi开发相关，那第一步肯定是如何来控制WiFi打开关闭状态，以及如何来监听其状态变化。

## WiFi连接控制类--WiFiManager

![](http://upload-images.jianshu.io/upload_images/5734256-4ef9dcec5c229bbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从官网参考可以知道它以下几个作用：
1. 获取配置好的网络，也就是之前连接过的WiFi；
2. 对当前活动的WiFi网络建立新连接或断开，可以查询有关网络状态；
3. 周围扫描到的接入点，对应类为ScanResult；
4. 定义了在Wi-Fi状态改变后发出广播的intent action。

可以看出主要是用它来控制监听WiFi状态

## 打开关闭WiFi
对WiFi进行控制要声明相关权限
```
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
```
接着获取WifiManager
```
mWifiManager= (WifiManager) getApplicationContext()
                .getSystemService(Context.WIFI_SERVICE);
```
注意这里必须使用Application的上下文，那在fragment中获取的话通过getActivity().getApplicationContext()，因为碎片中无法直接获取Application Context

获取到WifiManager后就可以通过isWifiEnabled()判断是否打开，setWifiEnabled(boolean enabled)打开或关闭wifi，它的返回值说明操作结果，可能操作失败
```
//set wifi switch state
        swwifi.setChecked(mWifiManager.isWifiEnabled());

        swwifi.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                mWifiManager.setWifiEnabled(isChecked);
            }
        });
```
这里简单的通过个switch显示和控制开关状态

![](http://upload-images.jianshu.io/upload_images/5734256-696d71cb43378bf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)

## WiFi状态监听
上面只是能在应用中控制wifi开关，但如果在外面wifi发生变化，应用内没监听的话，那相关显示就错误了

监听是通过WifiManager定义的一些action动态注册广播监听（只能动态注册）
```
//注册接收器
    private void registerBroadcast() {
        IntentFilter filter = new IntentFilter();
        filter.addAction(WifiManager.WIFI_STATE_CHANGED_ACTION);
        registerReceiver(new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                if (intent.getAction()== WifiManager.WIFI_STATE_CHANGED_ACTION) {
                    switch (intent.getIntExtra(WifiManager.EXTRA_WIFI_STATE, WIFI_STATE_UNKNOWN)) {
                        case WIFI_STATE_DISABLED:{
                            Toast.makeText(context, "WiFi disabled", Toast.LENGTH_SHORT).show();
                            swwifi.setChecked(false);
                            break;
                        }
                        case WIFI_STATE_DISABLING:{
                            Toast.makeText(context, "WiFi disabling", Toast.LENGTH_SHORT).show();
                            break;
                        }
                        case WIFI_STATE_ENABLED :{
                            Toast.makeText(context, "WiFi enabled", Toast.LENGTH_SHORT).show();
                            swwifi.setChecked(true);
                            break;
                        }
                        case WIFI_STATE_ENABLING:{
                            Toast.makeText(context, "WiFi enabling", Toast.LENGTH_SHORT).show();
                            break;
                        }
                        case WIFI_STATE_UNKNOWN:{
                            Toast.makeText(context, "WiFi state unknown", Toast.LENGTH_SHORT).show();
                            break;
                        }
                    }
                }
            }
        }, filter);
    }
```
其他一些状态也基本是通过动态注册广播监听

![](http://upload-images.jianshu.io/upload_images/5734256-4d10aed03ea56643.gif?imageMogr2/auto-orient/strip)

上面有一点，刚打开注册广播时立即收到一条当前状态的广播
