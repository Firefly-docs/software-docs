# Qt cross compilation environment support

Firefly released two Qt cross-compilation tool chains, suitable for the following environments:

- Qt: 5.12.2
- Host: x86-64 / Ubuntu 18.04
- Target: Firefly RK3568 RK3566 RK3399 RK3328 PX30 / Ubuntu 18.04 Minimal&Desktop

and

- Qt: 5.15
- Host: x86-64 / Ubuntu 20.04
- Target: Firefly RK3588 RK3568 RK3566 / Ubuntu 20.04 Desktop

The tool chain fully supports wenEngine and backends such as EGLFS LinuxFB XCB.

* download link
```
Link: https://drive.google.com/drive/folders/1QF9T-KY-LyQyJy1e-9yrqmryz-avQ8Qj?usp=sharing
```
* Deployment

**See Qt5.1x.x_Release.md in tool-chain package for details**

**Note that the names of all paths in the document cannot be changed, otherwise it will cause compilation or running errors.**

* Compile

On the host side, enter the Qt project directory, `qmake && make`.

* Run

Demos are provided in the tool chain. After the deployment is completed, the user can build the demo on the host side and run the demo on the tartget side to test whether the deployment is successful.

After deciding which backend to use, you can modify the /etc/profile.d/target_qtEnv.sh in target, uncomment the environment variables of corresponding backend to keep it in effect.
```bash
# For example, using XCB, then uncomment the XCB part

#XCB
export QT_QPA_PLATFORM=XCB
export QT_QPA_EGLFS_INTEGRATION=XCB_EGL
```
