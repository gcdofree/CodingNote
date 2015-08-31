# AndroidNote
Android开发整理

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

*	[Android常用权限说明](#permission)
*	[Android开发tips](#development)
*	[Android系统知识](#system)
*	[Android界面、布局相关](#UI&Layout)
*	[Android相机、Bitmap、Canvas绘制相关](#Camera&Bitmap&Canvas)
*	[Android中Activity相关](#activity)
*	[Android中存储，读写相关](#store&IO)
*	[Android中消息提示相关](#message)
*	[Android中线程相关](#thread)
*	[Android中BroadcastReceiver](#broadcastreceiver)
*	[Android中Service](#service)
*	[Android中音频播放](#audio)

<h4 id="permission">Android常用权限说明</h4>

    <!-- 这个权限用于进行网络定位，包括wifi、基站-->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"></uses-permission>
    <!-- 这个权限用于访问GPS定位-->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"></uses-permission>
    <!-- 用于访问wifi网络信息-->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"></uses-permission>
    <!-- 获取运营商信息-->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission>
    <!-- 这个权限用于获取wifi的获取权限-->
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"></uses-permission>
    <!-- 用于读取手机当前的状态-->
    <uses-permission android:name="android.permission.READ_PHONE_STATE"></uses-permission>
    <!-- 向扩展卡写入数据-->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>
    <!-- 访问网络-->
    <uses-permission android:name="android.permission.INTERNET"></uses-permission>
    <!—SD卡读取权限-->
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"></uses-permission>
    <!--允许应用读取低级别的系统日志文件 -->
    <uses-permission android:name="android.permission.READ_LOGS"></uses-permission>
    <!--允许应用接受开机完成的广播，用于开机自启动 -->
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"></uses-permission>

<h4 id="development">Android开发tips</h4>
*   Android Support Library 是保证高版本的SDK在开发时的向下兼容性，比如4.x的Fragment可以用在1.6的版本上。常用版本是Android       Support v4（照顾1.6及以上版本，eclipse中默认带有）
*   9.png（适用于图片的缩放） WebP（Android 4.0之后原生支持，比png图片的无损压缩率更高）
*   多使用lint工具优化代码
*   如果Android程序（包含资源）需要导出jar包，可以首先生成apk，然后把apk复制到assets文件夹下（仅保留apk中的res文件夹和Android     Manifest），然后再打包（打包时不包含res文件夹）
*   dp 密度无关像素，无论屏幕密度如何，都可以得到同样尺寸；sp 缩放无关像素，常用于字体大小（和系统设置的字体大小一致）
*   Android中调用外部动态链接库（.so文件, shared object）时，方法为System.loadLibrary("test") 对应查找的文件为libtest.so。。Java通过JNI和其他语言（C++）的动态库进行交互，首先声明native方法，如public native void getNum() 然后生成class文件，再生成对应的.h头文件，然后包含头文件并用C++实现方法，最后生成.so动态库。在用C++实现时，需要引入jni.h头文件，同时传入的参数为jboolean, jintArray, jobject，通过jniEnv进行处理


<h4 id="system">Android系统知识</h4>
Android开机加电引导流程，分为6个步骤
*   Boot ROM: 引导boot媒介寻找boot loader的第一阶段，找到后开始执行boot loader
*   Boot Loader: 区别于Linux内核程序，用于初始化内存并将Linux内核装载到内存中
*   Kernel: Linux内核程序用于初始化硬件驱动，内存管理（虚拟内存）
*   Init: 初始化进程，文件系统
*   Zygote: 初始化并运行Dalvik虚拟机
*   System Service: 启动所有的Android系统服务，如电话，蓝牙，wifi

Dalvik虚拟机和Java虚拟机的区别
*   文件类型：Dalvik使用的是dex文件，JVM是class文件。一个dex文件可以包含多个类，而class只含有一个类，所以可以对重复出现的字符     串和常数只保存一次，节省空间，适用于内存和处理器性能有限的手机上。用DX工具可以把多个class编译为dex
*   指令集：Dalvik的指令集是基于寄存器的，而JVM是基于栈的。基于栈的指令集比较紧凑，指令集较短；基于寄存器的指令集比较长。如JV     M的指令只占一个字节，所以是字节码。执行同样的功能，基于栈的需要执行更多指令，而基于寄存器的需要更多指令空间。更多的指令意     味着更长的占用CPU时间，更多的指令空间意味着缓存容易失效

<h4 id="UI&Layout">Android界面、布局相关</h4>
*   五大布局: FrameLayout（所有空间堆叠到左上角，适用于layout中只有一个view），LinearLayout（线性布局），AbsoluteLayout（绝对     布局），RelativeLayout（相对布局），TableLayout（表格布局）
*   ListView调用Adaptor的getView方法获取每个子项item视图
*   LinearLayout中的layout_weight作用于剩余空间extra space，根据组件的weight比例进行分配
 
        // 假设在一个LinearLayout中有两个view，各自的width为w1、w2，parent的width为w
        layout_width = "0dp"; // 此时w1=w2=0，剩余空间是w，如果weight比是1:2，实际width比例是w1:w2 = (0+1/3):(0+2/3) = 1:2
        layout_width = "match_parent"; // 此时w1=w2=w，剩余空间是-w，如果weight比是1:2，实际width比例是w1:w2 = (1-1/3):(1-2/3) = 2:1
*   在旋转屏幕后，会将当前的activity销毁，然后重新生成，需要重写onSaveInstanceState(Bundle)方法，保存临时数据；旋转之后，系统     默认会调用layout-land中的同名layout文件

<h4 id="Camera&Bitmap&Canvas">Android相机、Bitmap、Canvas绘制相关</h4>
*   Android拍照

        Camera.takePicture(ShutterCallback, PictureCallbackRaw, PictureCallbackJpeg); 
        Camera.parameters.setPictureSize(w, h); // 设置照片尺寸
*   Bitmap相关操作

        // 生成bitmap的四种方式
        Bitmap bitmap = null;
        bitmap = BitmapFactory.decodeByteArray(data, offset, length, options);
        bitmap = BitmapFactory.decodeFile(path, options);
        bitmap = BitmapFactory.decodeResource(res, id, options);
        bitmap = BitmapFactory.decodeStream(is, outPadding, options);
        // 仅获取图片的width和height，而不将图片载入内存，不会生成bitmap，返回null
        options.inJustDecodeBounds = true;
        // 获取一个缩略图，节省内存，可用于显示小图的模式
        options.inSampleSize = 2; // 2000*1000 -> 1000*500用于获取一个缩略图片
        // 根据已有bitmap进行scale
        createScaledBitmap(Bitmap src, int dstWidth, int dstHeight, boolean filter);
        // 图片使用完成后，调用recycle回收资源
        bitmap.recycle();
        
<h4 id="activity">Android中Activity相关</h4>
Activity四种启动模式：standard（每次启用新的实例）, singleTop（如果在栈顶，则复用；否则启用新的实例）, singleTask, singleInstance

*   任务Task是一系列相关的Activity组成
    
*   启动singleTask模式的Activity时，会在系统中搜寻是否已经存在一个合适的任务，若存在，则会将这个任务调度到前台以重用这个任务     。如果这个任务中已经存在一个要启动的Activity的实例，则清除这个实例之上的所有Activity，将这个实例显示给用户。如果这个已存     在的任务中不存在一个要启动的Activity的实例，则在这个任务的顶端启动一个实例。若这个任务不存在，则会启动一个新的任务，在这     个新的任务中启动这个singleTask模式的Activity的一个实例
*   启动singleInstance的Activity时，会在系统中搜寻是否已经存在一个这个Activity的实例，如果存在，会将这个实例所在的任务调度到     前台，重用这个Activity的实例（该任务中只有这一个Activity），如果不存在，会开启一个新任务，并在这个新任务中启动这个singleI     nstance模式的Activity的一个实例，独占这个task

startActivity是系统方法，其中intent用来在不同的activity之间通信：在父activity中，有startActivityForResult和onActivityResult；在子activity中，有setResult方法

在activity中使用fragment
*   需要在布局中为fragment视图安排位置，同时还需要管理fragment实例的生命周期（由托管的activity控制，而不是系统）
*   fragment有onCreate是public，而activity是protected
*   fragment初始化视图的方法是onCreateView，用inflator去加载layout文件
*   FragmentManager: 用来管理fragment队列和fragment事务，获取方法是getFragmentManager()
*   通过findFragmentByID或者findFragmentByTag来获取fragment
*   一个Activity对应于一个BackStack，当用户按返回键时，系统会检查BackStack中fragment的顺序。fragment可以存放在backStack中，通过addToBackStack方法入栈，popBackStack方法出栈。按返回键时，优先检查Activity对应的BackStack中有没有fragment，当把所有栈中的fragment弹出后，再依次根据activity对应的栈中顺序返回activity
*   FragmentTransaction，包含add, remove,  replace方法操作fragment，最后调用commit，commit并不是立即执行，而是把事务transaction加入到对应activity的UI线程队列中
*   fragment生命周期：onAttach, onCreate, onCreateView, onActivityCreated，与托管的activity状态一致，onDestroyView, onDestroy, onDetach

<h4 id="store&IO">Android中存储，读写相关</h4>
Sharedpreferences 进行保存时，commit是同步写到存储介质上，并返回true or false；apply是异步写到存储介质上，无返回值，效率比commit高

<h4 id="message">Android中消息提示相关</h4>
Toast 无法自定义duration，只能是LENGTH_SHORT或者LENGTH_LONG

<h4 id="thread">Android中线程相关</h4>
在主线程中，系统已经自动调用了Looper.prepare()方法，但是在子线程中创建handler时，则需要在创建之前添加Looper.prepare()，在创建完成后添加Looper.loop()，相当于把子线程变成loop循环线程。一个线程对应一个looper，一个looper对应一个Message Queue（源码Looper类中仅有一个Thread和MQ对象），一个MQ可以对应多个Handler和多个Message。消息由handler发送，源码的sendMessage方法里会有message.target = this，MQ通过识别target让对应的handler处理消息

<h4 id="broadcastreceiver">Android中BroadcastReceiver</h4>

*   BroadcastReceiver在执行完onReceive方法之后，就会被销毁
*   Android实现开机启动时，需要定义一个BroadcastReceiver，用来接受开机完成的广播，同时也要申请对应的权限。在自定义的BroadcastReceiver的onReceive方法中，就可以启调对应的Activity（context.startActivity）或者Service（context.startService）。首次安装应用后需要打开应用激活，才能在下一开机时自启动

<h4 id="service">Android中Service</h4>

*   Service是后台机制，不依赖界面，并不是线程
*   Service运行在主线程中，所以如果直接执行耗时操作，程序会无响应（可以在service中开启子线程）。Service的生命周期包括onCreate，onStartCommand，onDestroy。通过Intent intent = new Intent(this, MyService.class); startService(intent);来启动/结束service。onCreate方法只在Service被创建时调用，onStartCommand方法在每次开启service的时候都会被调用
*   Activity中可以的service通信，包括绑定和解绑service。在Activity中通过bindService方法绑定对应的service和serviceConnection，同时需要在service中的onBind方法中返回一个Binder对象，这个对象中的方法就可以在Activity中通过serviceConnection被调用
*   一个service只有在处理停止以及没有和任何Activity绑定的情况下才能被销毁。后台运行的service可能被销毁，此时可以使用前台service（带有状态栏显示，和notification对象绑定，优先级更高），或者把onStartCommand方法的返回值设置为start_redeliver_intent（被杀死后会重启service），而不是start_not_sticky（被杀死后被关闭）。可以使用定时器AlarmManager延迟并循环起调service，或者通过两个service互相起调

<h4 id="audio">Android中音频播放</h4>

在播放音乐时（MediaPlayer），有来电的话，需要停止播放音乐，并播放来电铃声。传统做法是获取来电的broadcast，然后进行相关处理，但是这个方法需要监听通话状态权限，并且只能针对来电进行处理。正确的做法是监听音频焦点（Audio Focus，监听器是OnAudioFocusChangedListener）。音频播放有很多流，如音乐流，电话声音流，播放音乐之前获取对应流的Audio Focus，结束播放时需要取消音频焦点。在播放时，如果监听到音频焦点丢失/重新获取，就可以暂停/继续播放音乐。当来电时，音频焦点从音乐流给到铃声流
