# 默认系统时间24小时制
## 代码修改的方式
```
diff --git a/frameworks/base/packages/SettingsProvider/res/values/defaults.xml b/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
index f95ecc6535..06055dba7b 100644
--- a/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
+++ b/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
@@ -244,4 +244,6 @@
 
     <!-- should show the screenshot button default -->
     <integer name="def_screenshot_button_show">0</integer>
+<!-- value 12/24/null 对应设置 12/24/自动-->
+    <string name="def_time_12_24" translatable="false">24</string>
 </resources>
diff --git a/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java b/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
index b3ff9d08a8..ebdd5d3637 100644
--- a/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
+++ b/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
@@ -2261,6 +2261,9 @@ class DatabaseHelper extends SQLiteOpenHelper {
    private void loadSystemSettings(SQLiteDatabase db) {
                                     ......
+            
+            loadStringSetting(stmt, Settings.System.TIME_12_24,
+                    R.string.def_time_12_24);
```

