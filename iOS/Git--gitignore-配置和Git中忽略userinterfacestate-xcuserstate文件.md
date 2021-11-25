###1.配置.gitigore
 在 [gitignore.io](https://www.gitignore.io) 输入你需要配置的语言，会帮助你自动生成一份配置。比如，输入Objective-C帮助你生成Objective-C语言gitignore的配置。



###2.CocosPods 忽略
CocosPods管理第三方会生成Podfile、Podfile.lock、Pods文件夹和.xcworkspace; 

| 文件     | 说明                   | 是否必须提交版本库|
|:--:|:--:|:--:|
| Podfile | 依赖配置文件 | 是 |
| Podfile.lock | 当前使用库的信息  | 可选 | 
| Pods文件夹 | 第三方缓存目录  | 否 |
| .xcworkspace | 工作空间  | 可选 |

按照上面的表显示规则, 需要设定忽略依赖库缓存目录Pods/  
```
#CocoaPods
Pods/ 
```

###3.忽略userinterfacestate.xcuserstate文件
在.gitignore文件中进行配置，所以只需要在工程中的代码目录下添加这个文件即可。
```
*.xcuserstate
project.xcworkspace
xcuserdata
UserInterfaceState.xcuserstate
project.xcworkspace/
xcuserdata/
UserInterface.xcuserstate
```
在你确定有这个文件并且加入了下面的忽略信息，那就需要清理缓存了。

清理这个缓存
git rm --cached [YourProjectName].xcodeproj/project.xcworkspace/xcuserdata/[YourUsername].xcuserdatad/UserInterfaceState.xcuserstate
或者直接找到你的工程目录project.xcworkspace，由于project.xcworkspace是一个包文件，直接显示包内容，找到UserInterfaceState.xcuserstate移到垃圾桶就行了。
然后打开把你的修改，commit，push。修改的过程中，最好把xcode退出，避免你删除UserInterfaceState.xcuserstate之后又会生产，造成忽略失败。


###4.最后的忽略文件如下
```

# Created by https://www.gitignore.io/api/objective-c

### Objective-C ###
# Xcode
#
# gitignore contributors: remember to update Global/Xcode.gitignore, Objective-C.gitignore & Swift.gitignore

## Build generated
build/
DerivedData/

## Various settings
*.pbxuser
!default.pbxuser
*.mode1v3
!default.mode1v3
*.mode2v3
!default.mode2v3
*.perspectivev3
!default.perspectivev3
xcuserdata/

## Other
*.moved-aside
*.xccheckout
*.xcscmblueprint

## Obj-C/Swift specific
*.hmap
*.ipa
*.dSYM.zip
*.dSYM

# CocoaPods - Refactored to standalone file
Pods/ 

*.xcuserstate
project.xcworkspace
xcuserdata
UserInterfaceState.xcuserstate
project.xcworkspace/
xcuserdata/
UserInterface.xcuserstate

# Carthage - Refactored to standalone file

# fastlane
#
# It is recommended to not store the screenshots in the git repo. Instead, use fastlane to re-generate the
# screenshots whenever they are needed.
# For more information about the recommended setup visit:
# https://docs.fastlane.tools/best-practices/source-control/#source-control

fastlane/report.xml
fastlane/Preview.html
fastlane/screenshots
fastlane/test_output

# Code Injection
#
# After new code Injection tools there's a generated folder /iOSInjectionProject
# https://github.com/johnno1962/injectionforxcode

iOSInjectionProject/

### Objective-C Patch ###

# End of https://www.gitignore.io/api/objective-c
```


原文链接：http://www.jianshu.com/p/daf58f0ad282
