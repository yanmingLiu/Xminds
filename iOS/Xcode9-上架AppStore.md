###一、提交ipa文件出现ERROR ITMS-90022问题解决方案

先上图

![90022.jpg](http://upload-images.jianshu.io/upload_images/2051176-fe9536f3335ada0e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/670)

网上搜索了一番，基本回到都是这样的：
>这个问题从显示出的错误中我们看到项目中缺少了120x120的PNG格式图片，解决方法有
1.在Assets.xcassets中添加120x120的图片。
2.打开Contents.json，添加 "filename" : "icon.png"
3.在info.plist里面配置Icon files


特么的Xcode8上刚更新上架了，一个个都说按图标填写了就好了，还有什么alpha通道，这些基本的都是没有问题的，不然之前也不会上架成功，先上Xcode8上架成功的APPIcon图片。


![WX20170921-095926@2x.png](http://upload-images.jianshu.io/upload_images/2051176-9fc49dc0c4acd632.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


同样的配置，同样的配方，Xcode9上面AppIcon多了一个1024*1024的App Store，添加后打包上传依然是90022问题，

其实原因是：CocoaPods的资源编译脚本在iOS11下出了点问题.需要修改脚本.[解决方案转载自](https://github.com/mythkiven/developTips)

方法1.手动修改：1.找到路径下文件，2.替换最后一行代码

![WX20170921-102849@2x.png](http://upload-images.jianshu.io/upload_images/2051176-bf16ab51b6908e56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![WX20170921-102928@2x.png](http://upload-images.jianshu.io/upload_images/2051176-87240fa9dc52aad4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
代码：
```
  printf "%s\0" "${XCASSET_FILES[@]}" | xargs -0 xcrun actool --output-format human-readable-text --notices --warnings --platform "${PLATFORM_NAME}" --minimum-deployment-target "${!DEPLOYMENT_TARGET_SETTING_NAME}" ${TARGET_DEVICE_ARGS} --compress-pngs --compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}" --app-icon "${ASSETCATALOG_COMPILER_APPICON_NAME}" --output-partial-info-plist "${BUILD_DIR}/assetcatalog_generated_info.plist"
```

----------------------------

方法2. 在Podfile 添加如下代码, 需要注意的是,将[工程名] 换成自己工程的名称  然后$pod install
```
post_install do |installer|
    copy_pods_resources_path = "Pods/Target Support Files/Pods-[工程名]/Pods-[工程名]-resources.sh"
    string_to_replace = '--compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}"'
    assets_compile_with_app_icon_arguments = '--compile "${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}" --app-icon "${ASSETCATALOG_COMPILER_APPICON_NAME}" --output-partial-info-plist "${BUILD_DIR}/assetcatalog_generated_info.plist"'
    text = File.read(copy_pods_resources_path)
    new_contents = text.gsub(string_to_replace, assets_compile_with_app_icon_arguments)
    File.open(copy_pods_resources_path, "w") {|file| file.puts new_contents }
end

```
