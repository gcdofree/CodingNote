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
*   System Service: 启动所有的android系统服务，如电话，蓝牙，wifi



