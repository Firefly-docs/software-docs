# FireflyApi2 installation upgrade

#### 1. Local ota package upgrade
```
Function：public void installPackage(String path)
Description：Restart the ota package upgrade. Currently, it only supports to put in the built-in storage root directory, i.e./sdcard/
Parameter：path Absolute path to the ota package
Example：
mFireflyApi2.installPackage("/sdcard/update.zip");
```

