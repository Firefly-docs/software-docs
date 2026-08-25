# Turn on or turn off the installation of unknown apps
## Code modification method 
### Android 7.1
In the `frameworks/base/packages/SettingsProvider/res/values/defaults.xml` file, modify the value of `def_install_non_market_apps`. 
* false : Close 
* true : Open 

### Android10 and above 
In Android10, the permanent authorization option of "Allow Unknown Sources" to install applications has been deleted. The switch is no longer found in the system settings. Google will modify the permanent authorization to a separate authorization each time. When the user installs a third party each time The source of the Android software requires manual confirmation of the software permissions. 

