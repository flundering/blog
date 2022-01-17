#使用VSCode高效阅读Android native代码
## 一、C/C++配置
### 1、安装软件
#### 1.1 安装VSCode
下载VSCode软件包[VSCode安装包](https://go.microsoft.com/fwlink/?LinkID=760868) 
安装：
```
 # Ubuntu 20.04+，可以使用apt命令进行安装
sudo apt install ./code_xxx.deb

 # Ubuntu 20.04-，则需要执行dpkg命令安装deb文件，并使用apt-get安装依赖
sudo dpkg -i ./code_xxx.deb && sudo apt-get install -f
```

#### 1.2 安装clangd或者ccls插件
clangd是llvm官方开发的language server，比较权威，但在某些场景下的使用体验不如私人维护的ccls，所以如果你PC系统是Ubuntu20.04+的话，更推荐ccls；较低版本的Ubuntu系统安装ccls可能比较繁琐，使用clangd也还不错。
```
sudo apt install clangd # 或在ubuntu 20.04+版本上，sudo apt install ccls
```
####1.3 安装vscode的clangd插件或ccls（推荐）插件
clangd/ccls插件是vscode和clangd/ccls之间的桥梁

打开VSCode之后在左侧选择扩展程序，直接搜索clangd/ccls并安装即可
#### 1.4 安装CMake
```
sudo apt install cmake
```
###2、下载Android源代码
```
$ mkdir venus-s && cd venus-s
$ repo init -u ssh://zhangshuai10@git.mioffice.cn:29418/platform/manifest.git -b bsp-android-s -m miui-pangu_8450-s.xml --repo-url=ssh://zhangshuai10@git.mioffice.cn:29418/tools/repo.git --no-repo-verify
$ repo sync -c -d --no-tags
```
###3、 生成Android C/C++代码的编译命令配置文件
####3.1 生成CMake配置文件
```
$ bash # 切换到bash
$ source build/envsetup.sh
$ export BUILD_TARGET_PRODUCT=venus
$ lunch missi-userdebug
$ export SOONG_GEN_CMAKEFILES=1
$ export SOONG_GEN_CMAKEFILES_DEBUG=1
$ make nothing
... # 等待10~20分钟
$ ls -l out/development/ide/clion #查看生成的Cmake配置文件
art     bootable  cmake-build-debug  cts     development  external    hardware  libnativehelper  packages          prebuilts  test   vendor
bionic  build     CMakeLists.txt  ...
```
####3.2 添加我们关注的模块
修改out/development/ide/clion/CMakeLists.txt
```
cmake_minimum_required(VERSION 3.6)
project(venus-s)

set(ANDROID_ROOT /mnt/miui/sn750/venus-s)

#######################################################################
# 下面是我关注的模块，你可以跟进自己的需要进行增删
#######################################################################
# libc++
add_subdirectory(${ANDROID_ROOT}/out/developmenreadStrongBinderadd_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/external/libcxx/libc++fs-arm64-android/)

# bionic(or libc)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/bionic/libc/libc_bionic-arm64-android/)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/bionic/libc/libstdc++-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/bionic/libc/libc_pthread-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/bionic/libc/async_safe/libasync_safe-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/bionic/libc/system_properties/libsystemproperties-arm64-android)

# libutils
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/core/libutils/libutils-arm64-android/)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/core/libutils/libutilscallstack-arm64-android/)

# libcutils
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/core/libcutils/libcutils-arm64-android/)

# libbase
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/libbase/libbase-arm64-android/)

# libbinder
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/native/libs/binderdebug/libbinderdebug-arm64-android/)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/native/libs/binder/libbinder-arm64-android)

# fw
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/miui/frameworks/base/core/jni/android_runtime/libmiui_runtime-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/services/incremental/service.incremental-arm64-android)
# HWUI
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/libs/hwui/libhwui-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/libs/hwui/hwuimacro-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/libs/hwui/hwuimicro-arm64-android)
# fw JNIs
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/core/jni/libandroid_runtime-arm64-android)
# fw others
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/libs/androidfw/libandroidfw-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/libs/input/libinputservice-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/libs/services/libservices-arm64-android)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/base/cmds/app_process/app_process-arm64-android)

# installd
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/frameworks/native/cmds/installd/installd-arm64-android/)

# vold
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/vold/vold-arm64-android/)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/vold/libvold_binder-arm64-android/)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/vold/libvold-arm64-android/)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/vold/vdc-arm64-android/)
add_subdirectory(${ANDROID_ROOT}/out/development/ide/clion/system/vold/vold_prepare_subdirs-arm64-android/)
cmake_minimum_required(VERSION 3.6)
```
#### 3.3 生成compile_commands.json
```
$ ln -s out/development/ide/clion/CMakeLists.txt .
$ cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1 .
$ ls -lh compile_commands.json # 查看生成的json文件
-rw-rw-r-- 1 pip pip 15M Oct 23 18:25 compile_commands.json
```
###4、到这里所有阅读C/C++代码的准备步骤就完成了，只需要打开源码目录就可以浏览Native源代码文件了
```
code {android-source code}
```

## 二、rust配置
c太原始，而c++又太复杂(lan)，[rust](https://doc.rust-lang.org/stable/rust-by-example/hello.html) 可能是系统底层程序编程的未来...
阅读资料：
[Google使用rust开发底层系统库背后的原因](https://security.googleblog.com/2021/04/rust-in-android-platform.html) 
[微软：we-need-a-safer-systems-programming-language](https://msrc-blog.microsoft.com/2019/07/18/we-need-a-safer-systems-programming-language/) 
[微软：why-rust-for-safe-systems-programming](https://msrc-blog.microsoft.com/2019/07/22/why-rust-for-safe-systems-programming/) 
####1、安装rust-analyzer插件，略
####2、安装rustup工具
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
####3、生成rust项目的编译数据库
```
$ export SOONG_GEN_RUST_PROJECT=1
$ make nothing
$ cp out/soong/rust-project.json .
```
####4、导入rust-project.json
```
$ cat .vscode/settings.json
{
  "rust-analyzer.linkedProjects": [
    "rust-project.json"
  ]
}
```
参考以上配置，在"rust-analyzer.linkedProjects"里指明rust-project.json文件的位置
####5、重启VSCode，既可以浏览rust代码了。
