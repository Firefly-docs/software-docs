# Setting of built-in default input method 
## Code modification method 
* Get the input method package name 

	```shell
    adb shell settings get secure enabled_input_methods
    com.android.inputmethod.latin/.LatinIME:com.iflytek.inputmethod.google/com.iflytek.inputmethod.FlyIME:com.google.android.inputmethod.pinyin/.PinyinIME
    adb shell settings get secure default_input_method
    com.iflytek.inputmethod.google/com.iflytek.inputmethod.FlyIME
    ```

* Modify the code as follows 

    ```
    diff --git a/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml b/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    index ba87da7ec4..7ed79b4c15 100644
    --- a/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    +++ b/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    @@ -19,4 +19,7 @@

         <!-- if 1 ,reboot will reset DEFAULT_LAUNCHER -->
         <bool name="def_lock_default_launcher">false</bool>
    +
    +       <string name="enabled_input_methods" translatable="false">com.android.inputmethod.latin/.LatinIME:com.iflytek.inputmethod.google/com.iflytek.inputmethod.FlyIME:com.google.android.inputmethod.pinyin/.PinyinIME</string>
    +       <string name="def_input_method" translatable="false">com.iflytek.inputmethod.google/com.iflytek.inputmethod.FlyIME</string>
     </resources>

    diff --git a/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java b/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
    index cac5f800bd..f705a895b4 100644
    --- a/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
    +++ b/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
    @@ -2474,6 +2474,11 @@ class DatabaseHelper extends SQLiteOpenHelper {
                 stmt = db.compileStatement("INSERT OR IGNORE INTO secure(name,value)"
                         + " VALUES(?,?);");

    +                       loadStringSetting(stmt, Settings.Secure.ENABLED_INPUT_METHODS,
    +                                       R.string.enabled_input_methods);
    +                       loadStringSetting(stmt, Settings.Secure.DEFAULT_INPUT_METHOD,
    +                                       R.string.def_input_method);
    +
                 loadStringSetting(stmt, Settings.Secure.LOCATION_PROVIDERS_ALLOWED,
                         R.string.def_location_providers_allowed);
    ```

