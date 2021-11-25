flutter打包报错记录：
- Unexpected duplicate tasks
  ```
  处理方法：
  修改项目编译，不使用New Build System，
  在File > Project/Workspace Settings中的
  Share Project/Workspace Settings 里build system 
  将New Build System(Default)切换成Legacy build system。
  ```

- ERROR: Flutter archive builds must be run in Release mode.
To correct, ensure FLUTTER_BUILD_MODE is set to release or run:

  ```
  这个错误来源于这行命令
  /bin/sh "$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build,
  你可以在Xcode->Build Phases-> Run Script中看到这行代码。
  如果打包时没有指定mode就会报错，
  需要在Build Settings中加入FLUTTER_BUILD_MODE字段，
  并根据不同环境设置为debug或release，一定要对应好，
  当设置为release时是无法在模拟器上运行的，所以不要在debug环境把这个字段设为release。

  Bitcode设置为NO，
  
  Preprocessor Macros添加
  FLUTTER_BUILD_MODE=Debug和FLUTTER_BUILD_MODE=Release，
  如果还有其他环境需要根据情况选择Debug或Release。
  ```
- "_FlutterMethodNotImplemented", referenced from:
      -[FluttertoastPlugin handleMethodCall:result:] in FluttertoastPlugin.o
  "_OBJC_CLASS_$_FlutterMethodChannel", referenced from:
      objc-class-ref in FluttertoastPlugin.o
ld: symbol(s) not found for architecture i386

  ```
  苹果iOS11后不再支持32位应用，
  具体操作是在Xcode->Target->Build Setting中
  将Build Active Architecture Only设为YES,
  并将Vaild Architecture设为[arm64, arm64e]
  ```

- ld: bitcode bundle could not be generated because Flutter' was built without full bitcode. 
clang: error: linker command failed with exit code 1 (use -v to see invocation)

  ```
  这个错误pods方式还有问题：
  以pods方式集成flutter, 还是未解决。
  https://github.com/flutter/flutter/issues/48092

  以framework的方式集成flutter，可以打包。
  ```

-  **IPA processing failed**
  ```
  iOS13苹果取消了32位系统的支持,
  而SDK包含i386 x86_64的架构，
  日志IDEDistribution.standard.log

  处理方法：
  在程序里加一个脚本，剔除禁止的架构
  TARGETS->Build Phases->New Run Script Phase
  脚本内容在Flutter官方issuse里面：
  https://github.com/flutter/flutter/issues/42917

```

-  **Flutterxxx.h file not found。**

   ```
   处理方法：
   每次拉取flutter代码，有插件或者依赖更新，
   都需要执行：
   flutter clean
   flutter pub get
   pods install
   ```  
- Flutter Framework添加后无法使用的问题。
  
  ```
  处理方法：
  编译设置的 General > Frameworks, Libraries, and Embedded Content 下，
  然后在 Embed 下拉列表中选择 “Embed & Sign”。

  编译设置中的 Framework Search Paths中
  必须对应设置debug release模式Framework路径
  ```


- Found an unexpected Mach-O header。
  
  ```
  插件会帮助你生成 静态或动态框架。
  静态框架应该直接链接而不是嵌入。
  如果你在应用中嵌入了静态框架，
  你的应用将不能发布到 App Store。

  编译设置的 General > Frameworks, Libraries, and Embedded Content 下，
  然后在 Embed 下拉列表中选择 “Embed & Sign”。
  ```

- flutter build iOS Error ： 
```
flutter build ios --release
```

看到错误信息中有Signing iOS app 并不是当前发布的app账号，
1. 删除keys中所有其他的账号证书，只保留发布账号，
2. .ios/runner工程中使用发布账号，修改了bundle id, 但是这个好像没有什么用，重新编译flutter的时候也会改为默认。
3. 根据issue修改
https://github.com/flutter/flutter/issues/18305
https://github.com/flutter/flutter/issues/24376
```
1.  open ios/Runner.xcodeproj
2.  I checked Runner/Pods is empty in Xcode sidebar.
3.  drop Pods/Pods.xcodeproj into Runner/Pods.
4.  "Valid architectures" to only "arm64" (I removed armv7 armv7s) 
[13364](https://github.com/flutter/flutter/issues/13364)
5. 只支持iOS平台，并设置只支持64位，Build Settings - Architectures - Supported Platforms = ios Valid Acrchitectures = arm64
6. 将New Build System(Default)切换成Legacy build system。第一个错误设置有详细操作

```

错误信息：

```
Encountered error while building for device.

或者

error: Building for iOS, but the embedded framework 'App.framework' was built for macOS. (in target 'Runner' from project 'Runner')
Encountered error while building for device.
```

-  NoMethodError - undefined method `size' for nil:NilClass 
- Error running pod install 
issues处理方法
[https://github.com/CocoaPods/CocoaPods/issues/8377](https://github.com/CocoaPods/CocoaPods/issues/8377)

```
// 清理的时候，如果是插件工程，一定要cd到主flutter工程和example工程 一起clean
flutter clean
rm -Rf ios/Pods
rm -Rf ios/.symlinks
rm -Rf ios/Flutter/Flutter.framework
rm -Rf ios/Flutter/Flutter.podspec

```

- Failed to find assets path for "Frameworks/App.framework/flutter_assets"
[https://github.com/flutter/flutter/issues/29974](https://github.com/flutter/flutter/issues/29974)

```
1- rm -rf ios/Flutter/App.framework

2- Change the Runner target build settings Build Phases > Thin Binary script as follows:

/bin/sh "$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" embed
/bin/sh "$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" thin
```


- iOS 热重置连不上问题
- iOS mDNS meta issue
- flutter installing and launching iOS 
解决办法[https://github.com/flutter/flutter/issues/46705](https://github.com/flutter/flutter/issues/46705)
```
系统偏好设置>网络> iPhone USB>取消选中“除非需要，否则禁用”>应用。

如果没有iPhone USB，则可能需要打开热点才能使其显示，以便取消选中该设置。
该设置在启用或不启用热点的情况下都适用，启用时将导致mDNS查找失败。
```


- flutter iOS 打包runner工程 ，修改DisplayName
info.plist里面添加CFBundleDisplayName 不是直接修改CFBundleName
```
<key>CFBundleDisplayName</key>
<string>显示的app名字</string>

<key>CFBundleName</key>
```
