### Activity
- 生命周期全解析
- 四种启动模式
- IntentFilter匹配规则

##### 生命周期
**Activity的生命周期?**

1.`onCreate()`

>状态：Activity 正在创建<br/>
任务：做初始化工作，如setContentView界面资源、初始化数据<br/>
注意：此方法的传参Bundle为该Activity上次被异常情况销毁时保存的状态信息

2.`onSreat()`

>状态：Activity 正在启动，这时Activity 可见但不在前台，无法和用户交互

3.`onResume()`

>状态：Activity 获得焦点，此时Activity 可见且在前台并开始活动

4.`onPause()`

>状态： Activity 正在停止<br/>
任务：可做数据存储、停止动画等操作<br/>
注意：Activity切换时，旧Activity的onPause会先执行，然后才会启动新的Activity

5.`onStop()`

>状态：Activity 即将停止<br/>
任务：可做稍微重量级回收工作，如取消网络连接、注销广播接收器等<br/>
注意：新Activity是透明主题时，旧Activity都不会走onStop<br/>

6.`onDestroy()`

>状态：Activity 即将销毁<br/>
任务：做回收工作、资源释放

7.`onResart()`

>状态：Activity 重新启动，Activity由后台切换到前台，由不可见到可见

_onStart()和onResume()、onPause()和onStop()的区别: onStart与onStop是从Activity是否可见这个角度调用的，onResume和onPause是从Activity是否显示在前台这个角度来回调的，在实际使用没其他明显区别_

**Activity生命周期的切换过程?**

1.启动一个Activity:

> onCreate()-->onStart()-->onResume()

2.打开一个新Activity:

>旧Activity的onPause() -->新Activity的onCreate()-->onStart()-->onResume()-->旧Activity的onStop()

3.返回到旧Activity:

>新Activity的onPause（）-->旧Activity的onRestart()-->onStart()-->onResume()-->新Activity的onStop()-->onDestory();

4.Activity1上弹出对话框Activity2:

>Activity1的onPause()-->Activity2的onCreate()-->onStart()-->onResume()

5.关闭屏幕/按Home键:

>Activity2的onPause()-->onStop()-->Activity1的onStop()

6.点亮屏幕/回到前台:

>Activity2的onRestart()-->onStart()-->Activity1的onRestart()-->onStart()-->Activity2的onResume()

7.关闭对话框Activity2:

>Activity2的onPause()-->Activity1的onResume()-->Activity2的onStop()-->onDestroy()

8.销毁Activity1:

>onPause()-->onStop()-->onDestroy()

**生命周期的各阶段?**

1.完整生命周期

>Activity在onCreate()和onDestroy()之间所经历的。
在onCreate()中完成各初始化操作，在onDestroy()中释放资源

2.可见生命周期

>Activity在onStart()和onStop()之间所经历的。
活动对于用户是可见的，但仍无法与用户进行交互

3.前台生命周期

>Activity在onResume()和onPause()之间所经历的。
活动可见，且可交互

4.onSaveInstanceState和onRestoreInstanceState

>a.出现时机：异常 情况下Activity 重建，非用户主动去销毁<br/>
>b.系统异常终止时，调用onSavaInstanceState来保存状态。该方法调用在onStop之前，但和onPause没有时序关系。

_onSaveInstanceState与onPause的区别：前者适用于对临时性状态的保存，而后者适用于对数据的持久化保存_
>c.Activity被重新创建时，调用onRestoreInstanceState（该方法在onStart之后），并将onSavaInstanceState保存的Bundle对象作为参数传到onRestoreInstanceState与onCreate方法。

_可通过onRestoreInstanceState(Bundle savedInstanceState)和onCreate((Bundle savedInstanceState)来判断Activity是否被重建，并取出数据进行恢复。但需要注意的是，在onCreate取出数据时一定要先判断savedInstanceState是否为空。另外，谷歌更推荐使用onRestoreInstanceState进行数据恢复。_

**Activity异常情况下生命周期分析?**

1.由于资源相关配置发生改变，导致Activity被杀死和重新创建<br/>

例如屏幕发生旋转：当竖屏切换到横屏时，会先调用onSaveInstanceState来保存切换时的数据，接着销毁当前的Activity，然后重新创建一个Activity，再调用onRestoreInstanceState恢复数据。

>onSaveInstanceState-->onPause（不定）-->onStop-->
onDestroy-->onCreate-->onStart-->onRestoreInstanceState-->onResume

_为了避免由于配置改变导致Activity重建，可在AndroidManifest.xml中对应的Activity中设置android:configChanges="orientation|screenSize"。此时再次旋转屏幕时，该Activity不会被系统杀死和重建，只会调用onConfigurationChanged。因此，当配置程序需要响应配置改变，指定configChanges属性，重写onConfigurationChanged方法即可。_

2.由于系统资源不足，导致优先级低的Activity被回收

>a.Activity优先级排序

_前台可见Activity>前台可见不可交互Activity（前台Activity弹出Dialog)>后台Activity（用户按下Home键、切换到其他应用）_

>b.当系统内存不足时，会按照Activity优先级从低到高去杀死目标Activity所在的进程
>c.若一个进程没有四大组件在执行，那么这个进程将很快被系统杀死



