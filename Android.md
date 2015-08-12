# AndroidNote
Android开发整理

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

#### Android常用权限说明
    <!-- 这个权限用于进行网络定位，包括wifi、基站-->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"></uses-permission>
    <!-- 这个权限用于访问GPS定位-->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"></uses-permission>
    <!-- 用于访问wifi网络信息，wifi信息会用于进行网络定位-->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"></uses-permission>
    <!-- 获取运营商信息，用于支持提供运营商信息相关的接口-->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission>
    <!-- 这个权限用于获取wifi的获取权限，wifi信息会用来进行网络定位-->
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"></uses-permission>
    <!-- 用于读取手机当前的状态-->
    <uses-permission android:name="android.permission.READ_PHONE_STATE"></uses-permission>
    <!-- 写入扩展存储，向扩展卡写入数据，用于写入离线定位数据-->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>
    <!-- 访问网络，网络定位需要上网-->
    <uses-permission android:name="android.permission.INTERNET"></uses-permission>
    <!—SD卡读取权限，用户写入离线定位数据-->
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"></uses-permission>
    <!--允许应用读取低级别的系统日志文件 -->
    <uses-permission android:name="android.permission.READ_LOGS"></uses-permission>

#### Android开发tips
*   Android Support Library 是保证高版本的SDK在开发时的向下兼容性，比如4.x的Fragment可以用在1.6的版本上。常用版本是Android Support v4（照顾1.6及以上版本，eclipse中默认带有）
*   9.png（适用于图片的缩放） WebP（Android 4.0之后原生支持，比png图片的无损压缩率更高）
*   多使用lint工具优化代码
*   如果Android程序（包含资源）需要导出jar包，可以首先生成apk，然后把apk复制到assets文件夹下（仅保留apk中的res文件夹和AndroidManifest），然后再打包（打包时不包含res文件夹）
*   dp 密度无关像素，无论屏幕密度如何，都可以得到同样尺寸；sp 缩放无关像素，常用于字体大小（和系统设置的字体大小一致）
*   Android中调用外部动态链接库（.so文件, shared object）时，方法为System.loadLibrary("test") 对应查找的文件为libtest.so

#### Android系统知识
Android开机加电引导流程，分为6个步骤
*   Boot ROM: 引导boot媒介寻找boot loader的第一阶段，找到后开始执行boot loader
*   Boot Loader: 区别于Linux内核程序，用于初始化内存并将Linux内核装载到内存中
*   Kernel: Linux内核程序用于初始化硬件驱动，内存管理（虚拟内存）
*   Init: 初始化进程，文件系统
*   Zygote: 初始化并运行Dalvik虚拟机
*   System Service: 启动所有的Android系统服务，如电话，蓝牙，wifi

Dalvik虚拟机和Java虚拟机的区别
*   文件类型：Dalvik使用的是dex文件，JVM是class文件。一个dex文件可以包含多个类，而class只含有一个类，所以可以对重复出现的字符串和常数只保存一次，节省空间，适用于内存和处理器性能有限的手机上。用DX工具可以把多个class编译为dex
*   指令集：Dalvik的指令集是基于寄存器的，而JVM是基于栈的。基于栈的指令集比较紧凑，指令集较短；基于寄存器的指令集比较长。如JVM的指令只占一个字节，所以是字节码。执行同样的功能，基于栈的需要执行更多指令，而基于寄存器的需要更多指令空间。更多的指令意味着更长的占用CPU时间，更多的指令空间意味着缓存容易失效

#### Android界面、布局相关
*   五大布局: FrameLayout（所有空间堆叠到左上角，适用于layout中只有一个view），LinearLayout（线性布局），AbsoluteLayout（绝对布局），RelativeLayout（相对布局），TableLayout（表格布局）
*   ListView调用Adaptor的getView方法获取每个子项item视图
*   LinearLayout中的layout_weight作用于剩余空间extra space，根据组件的weight比例进行分配
 
        // 假设在一个LinearLayout中有两个view，各自的width为w1、w2，parent的width为w
        layout_width = "0dp"; // 此时w1=w2=0，剩余空间是w，如果weight比是1:2，实际width比例是w1:w2 = (0+1/3):(0+2/3) = 1:2
        layout_width = "match_parent"; // 此时w1=w2=w，剩余空间是-w，如果weight比是1:2，实际width比例是w1:w2 = (1-1/3):(1-2/3) = 2:1

#### Android 相机、Bitmap、Canvas绘制相关
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
        
        

