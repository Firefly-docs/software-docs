# Hide the status bar by default 
## Temporary modification method 
In the Settings, just enable the option. 

`Settings`-->`Display`-->`Always hide the status bar`

### Code modification method 
* The revised content is as follows   

    ```
    diff --git a/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml b/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    index ba87da7ec4..e32c9699f9 100644
    --- a/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    +++ b/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    @@ -12,7 +12,7 @@
         <!-- should show the hidebar button default -->
         <bool name="def_hidebar_button_show">true</bool>
         <!-- always hide statusbar -->
    -    <bool name="def_always_hide_bar">false</bool>
    +    <bool name="def_always_hide_bar">true</bool>

         <!-- will set DEFAULT_LAUNCHER in frist boot -->
         <string name="def_default_launcher" translatable="false"></string>
    ```

