---
layout: post
title: Flutter 安装/运行/打包
summary: Useful Flutter Terminals Command in VSCode
categories: Flutter
technique: true
---

## | flutter 安装

1. 下载 [flutter SDK](https://flutter.dev/docs/get-started/install/macos)，放在自定义目录解压，记住当前路径。
2. 配置环境变量 `export PATH="$PATH:`pwd`/flutter/bin"` （pwd-上一步记住的路径）这个命令配置了 PATH 环境变量，且只会在你当前命令行窗口中生效。 如果想让它永久生效，请查看更新 PATH 环境变量。
3. 检查是否安装成功 `flutter -h`
4. 更新 PATH 环境变量: 1>`vim ~/.bash_profile`;2>文件中增加一行代码`export PATH="$PATH:`pwd`/flutter/bin"`;3>`source ~/.bash_profile`;

## | 连接模拟器

连接模拟器需要下载 Android SDK / java SDK，并配置环境变量

[安装 Android SDK / java SDK]

```javascript
// 配置 Android SDK 环境变量：
export PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin  // 防止原来的变量丢
export ANDROID_HOME=/Users/XXX/android-sdk
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/build-tools/29.0.3


// 配置 java SDK 环境变量：
JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk-14.0.2.jdk/Contents/Home"
export JAVA_HOME
CLASS_PATH="$JAVA_HOME/lib"
PATH=".$PATH:$JAVA_HOME/bin"
```

检查是否安装成功 `adb version`

**_vim 提示 command not found_**

1. 输入命令`export PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/X11R6/bin`，可以暂时使用 vim 等命令

**_连接模拟器_**

windows: adb connect 127.0.0.1:7555
mac: 22471

```javascript
// mac 查看端口号是否被占用
sudo lsof -i:22471
// 连接
sudo adb connect localhost:22471
// 设备offline，依次执行以下命令
adb kill-server
adb start-server
adb devices
// 后续开发使用，打开mumu模拟器，依次执行以下命令
adb kill-server
adb start-server
```

```javascript
运行 flutter run 报错解决：
1.Lost connection to device
`brew upgrade --fetch-HEAD usbmuxd`
2.libusbmuxd not installed
brew install --HEAD libplist
brew install --HEAD usbmuxd
3.再次运行`brew upgrade --fetch-HEAD usbmuxd`
4.flutter run 或者
flutter run --enable-software-rendering
// ------ 配置vscode launch.json
"configurations": [{
  "name": "new_flutter",
  "cwd": "new_flutter",
  "request": "launch",
  "type": "dart",
  "args": ["--enable-software-rendering", "-d", "all"] // 添加此行
}]
```

## | VSCode 新建项目

command + shift + P => flutter new project

## | terminals command

flutter create 创建项目 | flutter run 运行项目
flutter devices 列出所有连接的设备 | flutter emulators 模拟器列表

## | APP 打包（android）

1. 配置 app 图标：项目目录/android/app/src/main/res/ ； 不同像素密度分别配置对应的图片文件，png 格式，统一命名，例如 img_icon，方便下一步配置
2. 配置 APP 名称、图标和系统权限：项目目录/android/app/src/main/AndroidManifest.xml

```javascript
android: label = 'myflutter'; // 设置APP安装到手机上显示的名称，支持中文
android: icon = '@mipmap/img_icon'; // img_icon 为APP图标用到的图片名称，不需要输入文件格式后缀
```

3. APP 注册

- 生成 keystore：`keytool -genkey -v -keystore ~/key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias key`,执行命令后，根据提示输入信息，会生成 key.jks 文件（不要共享给别人）。在项目目录的 android 文件夹下创建一个名为 key.properties 的文件（这个 key.properties 文件也不要共享出去！！），并贴入以下代码：

```
storePassword=密码    // 输入上一步创建KEY时输入的 密钥库 密码
keyPassword=密码    // 输入上一步创建KEY时输入的 密钥 密码
keyAlias=key
storeFile=路径   // key.jks的存放路径
例如（密码和路径后不能加注释！）：
storePassword=123456
keyPassword=123456
keyAlias=key
storeFile=key.jks
// key.jks 存发那个路径为 android/app 目录下
```

- 配置 Key 注册：进入项目目录的/android/app/build.gradle 文件

```javascript
// 在 android {这一行前面，加入如下代码：
def keystorePropertiesFile = rootProject.file("key.properties")
def keystoreProperties = new Properties()
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
// 把如下代码：
buildTypes {
    release {
        signingConfig signingConfigs.debug
    }
}
// 替换成：
signingConfigs {
    release {
        keyAlias keystoreProperties['keyAlias']
        keyPassword keystoreProperties['keyPassword']
        storeFile file(keystoreProperties['storeFile'])
        storePassword keystoreProperties['storePassword']
    }
}
buildTypes {
    release {
        signingConfig signingConfigs.release
    }
}
// 到defaultConfig中，录入APPID和版本号
defaultConfig {
  applicationId "com.example.myflutter"  //APPID
  minSdkVersion 16  //可运行应用的最低版本的 Android 平台，由该平台的 API 级别标识符指定
  targetSdkVersion 27  //指定运行应用的目标 API 级别。在某些情况下，这允许应用使用在目标 API 级别中定义的清单元素或行为，而不是仅限于使用那些针对最低 API 级别定义的元素或行为。
  versionCode 1    //内部版本号
  versionName "1.0"  //对外公布的版本号
  testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
}
```

- 编译生成 apk 文件：`flutter build apk`。执行成功后根据目录，找到生成的 apk 文件，即打包完成了。可以在终端输入命令安装 APP：`flutter install`（先卸载旧版再安装新版）

## | 插件安装

Dart | Flutter | Bracket Pair Colorizer | Flutter Widget Snippets | Awesome Flutter Snippets

## | MORE

[VSCode 下 Flutter 常用终端命令行](https://www.cnblogs.com/lxlx1798/p/11049922.html)

