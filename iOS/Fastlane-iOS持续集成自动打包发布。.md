
[fastlane](https://docs.fastlane.tools/)是为您的 iOS 和 Android 应用程序自动化测试部署和发布的最简单方法。🚀 它可以处理所有繁琐的任务，例如生成屏幕截图、处理代码签名和发布您的应用程序。

[fastlane官网](https://docs.fastlane.tools/)
[youtube](https://www.youtube.com/watch?v=Edr88s5YlH4)

###1：Fastlane 功能大全

![Fastlene.png](https://upload-images.jianshu.io/upload_images/2051176-0ed797971ce48dea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1024)

###2：iOS的Fastlane|安装Fastlane|向FastlaneProduce注册应用

####👉2.1 环境安装
*Homebrew 👉  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
*Ruby           👉  brew install ruby
*Bundler      👉  gem install bundler
*Fastlane     👉 brew install fastlane
*Git 	            👉  brew install git
*xCode命令行👉  xcode-select --install    

![Environment.png](https://upload-images.jianshu.io/upload_images/2051176-e1e9cd7bc04c2e8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####👉2.2 设定Fastlane
*Fastlane初始化 👉 fastlane init 

![Settihg.png](https://upload-images.jianshu.io/upload_images/2051176-bb03c635f762a710.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####👉2.3 Produce （有2总方式，1. 命令行 2. Fastfile 自定义lane文件配置）
![Oroduce.png](https://upload-images.jianshu.io/upload_images/2051176-675e07ff2b79a35b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####2.3.1命令行 👉 fastlane produce
![produce.png](https://upload-images.jianshu.io/upload_images/2051176-bcdc7049e49c5bcd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####2.3.1 Fastfile自定义lane文件配置 👉 fastlane register_app
![oroduce.png](https://upload-images.jianshu.io/upload_images/2051176-047bafac6fb868ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###3：iOS的Fastlane| Cert和Sigh  签名(certificates)和配置文件(provisioning profiles)
![Fastlane tor ios.png](https://upload-images.jianshu.io/upload_images/2051176-db73c508955a6b45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####3.1 创建证书和签名文件
👉 fastlane cert
👉 fastlane sigh
####3.2 创建lane通道，相同的参数可以提取到appfile中 后
👉 fastlane get_dev_certs

![default platfora( ios).png](https://upload-images.jianshu.io/upload_images/2051176-85e2c3a7ce78b80b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4：iOS的Fastlane| Match 证书管理
![Git repo.png](https://upload-images.jianshu.io/upload_images/2051176-6d03b27676d1666a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####4.1 设置match  git仓库
👉 fastlane match init
```Ruby
$ fastlane match init    
[✔] 🚀 
[16:28:30]: fastlane match supports multiple storage modes, please select the one you want to use:
1. git
2. google_cloud
3. s3
?  1
[16:28:32]: Please create a new, private git repository to store the certificates and profiles there
[16:28:32]: URL of the Git Repo: https://github.com/yanmingLiu/HJStoreBCertificate.git
[16:28:59]: Successfully created './fastlane/Matchfile'. You can open the file using a code editor.
[16:28:59]: You can now run `fastlane match development`, `fastlane match adhoc`, `fastlane match enterprise` and `fastlane match appstore`
[16:28:59]: On the first run for each environment it will create the provisioning profiles and
[16:28:59]: certificates for you. From then on, it will automatically import the existing profiles.
[16:28:59]: For more information visit https://docs.fastlane.tools/actions/match/
```

❌这里注意可能会给你报这个错误，是因为github用新的个人访问令牌替换原始的密码。按照[github文档](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)创建一个个人访问令牌来代替命令行或 API 中的密码。然后删除Matchfile，重新fastlane match init克隆输入密码的时候用最新的令牌就好了。
```Ruby
[15:35:42]: Cloning remote git repo...
[15:35:42]: If cloning the repo takes too long, you can use the `clone_branch_directly` option in match.
Cloning into '/var/folders/yd/wrnrn3s94s174b6q9ktk60zm0000gn/T/d20211026-3696-ga67md'...
fatal: could not read Username for 'https://github.com': terminal prompts disabled
[15:35:43]: Exit status: 128
[15:35:43]: Error cloning certificates repo, please make sure you have read access to the repository you want to use
[15:35:43]: Run the following command manually to make sure you're properly authenticated:
[15:35:43]: $ git clone https://github.com/yanmingLiu/HJStoreBCertificate.git /var/folders/yd/wrnrn3s94s174b6q9ktk60zm0000gn/T/d20211026-3696-ga67md

[!] Error cloning certificates git repo, please make sure you have access to the repository - see instructions above

```



####4.2 matchFile文件生成后，配置development appstore adhoc 所有的开发需要的签名资料都在git仓库中了
👉 fastlane match development
👉 fastlane match appstore
👉 fastlane match adhoc
现在证书管理全部上传到git仓库中了，并生成了如何在新机器上使用的README.md。



####4.3 添加新设备通道
####4.3.1 matchfile中添加force_for_new_devices(true)
![9it.url(gitegithus.coeirgohongthastutorial-certifAcate.gkt”).png](https://upload-images.jianshu.io/upload_images/2051176-ab531cba3ed756c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####4.3.2 新加同步设备通道 和 新创建一个Devicefile存储设备
👉  cd  fastlane
👉  touch Devicefile
👉 fastlane  sync_all_development
![Device Nane.png](https://upload-images.jianshu.io/upload_images/2051176-2b10c179839a1a39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![default platform(1os).png](https://upload-images.jianshu.io/upload_images/2051176-d8f525823a0e0c4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#####[Fastfile]() 因为之前是先有项目在集成fastlane 2、3步并不是用这个方式生成的。有2个lane 一个是release自动打包并上传appstore ,一个是pgyer_debug自动打包并上传蒲公英分发。
```Ruby
# This file contains the fastlane.tools configuration
# You can find the documentation at https://docs.fastlane.tools
#
# For a list of all available actions, check out
#
#     https://docs.fastlane.tools/actions
#
# For a list of all available plugins, check out
#
#     https://docs.fastlane.tools/plugins/available-plugins
#

# Uncomment the line if you want fastlane to automatically update itself
# update_fastlane

default_platform(:ios)

platform :ios do

  desc "Push a new release build to the App Store"
  lane :release do
    increment_build_number(xcodeproj: "HJStoreB.xcodeproj")
    build_app(workspace: "HJStoreB.xcworkspace", scheme: "HJStoreB")
    upload_to_app_store
  end


  # debug测试包
  desc "Push a new debug build to the pgyer.com"
  ipa_dir  = "fastlane_build/"
  ipa_name = "debug" + Time.new.strftime('%Y-%m-%d_%H:%M')

  lane :pgyer_debug do |options|
    gym(
    # 打包前clean项目
    clean: true, 
    # 构建时，隐藏不必要的信息  
    silent: true,           
    # bitcode
    include_bitcode: false,                             
    # 导出方式 app-store、ad-hoc、enterprise、development
    export_method: "ad-hoc",        
    # scheme    
    scheme: "HJStoreB",                 
    # 环境 Debug、Release
    configuration: "Debug",             
    # ipa的存放目录
    output_directory: "/Users/lym/Desktop/Archive",
    # 输出ipa的文件名为当前的build号
    output_name: ipa_name + get_build_number                
    )

    # 蒲公英
    pgyer(
      api_key: "4123246b31d0023aa348e8ee0cc7f987", 
      user_key: "e3bf8adb2f886e1557bfc7284d4e27ac",
      update_description:  options[:desc]
      )

  end

  # 同步设备
  lane :sync_all_development do
    sync_device_info
    match(type: "development")
  end

  lane :sync_device_info do
    register_devices(
      devices_file:"fastlane/Devicefile"
      )
  end

end
```
#####[Devicefile]() 需要按苹果官网的方式，试了一下自己写的文件中间的空格不管是tab还是4个空格，在执行fastlane sync_all_development会报错，最后还是下载苹果的[samples文件](https://developer.apple.com/account/resources/downloads/Multiple-Upload-Samples.zip)复制进来的。示例：

```Ruby
Device ID	Device Name	Device Platform
A123456789012345678901234567890123456789	NAME1	ios
B123456789012345678901234567890123456789	NAME2	ios
A5B5CD50-14AB-5AF7-8B78-AB4751AB10A8	NAME3	mac
A5B5CD50-14AB-5AF7-8B78-AB4751AB10A7	NAME4	mac
```
