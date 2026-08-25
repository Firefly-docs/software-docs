# Startup application 
## Code modification method 
### Non-launcher 
* Add the following content to the `AndroidManifest.xml` in the application source code 

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
* Add the code for broadcast reception `BootBroadcastReceiver.java`, such as 

    ```
    package com.example.testfile;

    import android.content.BroadcastReceiver;
    import android.content.Context;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.Toast;

    public class BootBroadcastReceiver extends BroadcastReceiver{
        private static final String TAG = "sjft";

        public static final String EXTRA_VOLUME_STATE =
                "android.os.storage.extra.VOLUME_STATE";

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
                    Intent launchIntent = new Intent(context, MainActivity.class);//Restart the app 
                    //If you don’t want to write the startup Activity, you can also get the default startup Activity by the following method 
                    //Intent launchIntent = context.getPackageManager().getLaunchIntentForPackage(packageName);
                    launchIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    context.startActivity(launchIntent);
                }
            } else if (action.equals("android.intent.action.BOOT_COMPLETED")){
                String packageName = intent.getData().getSchemeSpecificPart();
                Log.v(TAG,"BootBroadcastReceiver packageName:"+packageName);
                if(context.getPackageName().equals(packageName)){
                    Intent launchIntent = new Intent(context, MainActivity.class);//Restart the app 
                    //If you don’t want to write the startup Activity, you can also get the default startup Activity by the following method 
                    //Intent launchIntent = context.getPackageManager().getLaunchIntentForPackage(packageName);
                    launchIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    context.startActivity(launchIntent);
                }
            } 
        }
    }
    ```

* app system signature ([signature file](system_signature.md))  
* start app once
### Launcher
* Delete or not compile the Launcher application in the SDK 
* Add the following content to the first activity started in `AndroidManifest.xml` in the application source code 

    ```
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    ```

