# 开机启动应用
## 修改代码的方式
### 非 Launcher
* 在应用源码里的`AndroidManifest.xml`添加如下内容

    ```
    package="com.example.testfile"
    android:sharedUserId="android.uid.system">
    ```

	```
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    ```

    ```
    <receiver android:name="com.example.testfile.BootBroadcastReceiver" >
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <category android:name="android.intent.category.LAUNCHER" />
       </intent-filter>
    </receiver>
    ```

* 增加广播接收的代码`BootBroadcastReceiver.java`，比如

    ```
    package com.example.testfile;

    import android.content.BroadcastReceiver;
    import android.content.Context;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.Toast;

    public class BootBroadcastReceiver extends BroadcastReceiver {
        private static final String TAG = "sjft";

        public static final String EXTRA_VOLUME_STATE = "android.os.storage.extra.VOLUME_STATE";

        public static final int STATE_UNMOUNTED = 0;
        public static final int STATE_CHECKING = 1;
        public static final int STATE_MOUNTED = 2;
        public static final int STATE_MOUNTED_READ_ONLY = 3;
        public static final int STATE_FORMATTING = 4;
        public static final int STATE_EJECTING = 5;
        public static final int STATE_UNMOUNTABLE = 6;
        public static final int STATE_REMOVED = 7;
        public static final int STATE_BAD_REMOVAL = 8;

        @Override
        public void onReceive(Context context, Intent intent) {
            // TODO Auto-generated method stub
            String action = intent.getAction();
            if (action.equals("android.intent.action.PACKAGE_REPLACED")){
                String packageName = intent.getData().getSchemeSpecificPart();
                Log.v(TAG,"BootBroadcastReceiver packageName:"+packageName);
                if(context.getPackageName().equals(packageName)){
                    Intent launchIntent = new Intent(context, MainActivity.class);//重新启动应用
                    //此处如果不想写死启动的Activity，也可以通过如下方法获取默认的启动Activity
                    //Intent launchIntent = context.getPackageManager().getLaunchIntentForPackage(packageName);
                    launchIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    context.startActivity(launchIntent);
                }
            } else if (action.equals("android.intent.action.BOOT_COMPLETED")) {
                Intent launchIntent = new Intent(context, MainActivity.class);//重新启动应用
                //此处如果不想写死启动的Activity，也可以通过如下方法获取默认的启动Activity
                //Intent launchIntent = context.getPackageManager().getLaunchIntentForPackage(packageName);
                launchIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                context.startActivity(launchIntent);
            }
        }
    }
    ```

* 开机自启动 apk 需要系统签名（[系统签名文件](system_signature.md)）
* 必须先打开一次开机自启动 apk，才能接收到开机完成的广播

### Launcher
* 删除或者不编译 SDK 里的 Launcher 应用
* 在应用源码里的`AndroidManifest.xml`里最先启动的 Activity 添加如下内容

    ```
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    ```

