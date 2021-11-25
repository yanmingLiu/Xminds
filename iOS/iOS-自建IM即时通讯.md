###一、原由

其实现在有很多大厂IM的第三方，云信、融云、TIM，本人都有在项目中集成过，第三方好处就是你不用太多关注底层实现，demo里面几乎有完整的使用案例，麻烦的地方就是根据公司业务、UI不同，第三方IM库实现起来总是有很多麻烦。我们自建IM选择的是WebScoket协议和ProtocolBuffer的传输格式实现IM，iOS Android H5都需要聊天功能呢。

我在这里记录一下实现过程，包括：框架选择、protobuf使用、UI实现、数据库、业务集成。

###二、WebScoket + ProtocolBuffer 实现IM

#### 2.1框架选择：

WebSocket框架：[SocketRocket](https://github.com/facebook/SocketRocket)

ProtocolBuffer框架：[protobuf](https://github.com/protocolbuffers/protobuf)

数据库框架:[wcdb](https://github.com/Tencent/wcdb)

### 2.2protobuf使用

1.创建 proto 文件：一般后台创建好、前端只需要转换成对应语言。

2.mac环境设置：
2.1 下载、解压protobuf文件：  https://github.com/protocolbuffers/protobuf/releases
2.2 Protocol Buffers编译时使用一些其他工具需要手动安装，终端顺序执行下面后就可以转换proto文件为ObjC文件了。

安装Homebrew, 如果有直接跳过，一般mac环境都有。
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
安装 Protocol Buffer 依赖 工具
```
brew install autoconf automake libtool
```

cd 进入 protobuf解压后的文件夹依次执行
```
./autogen.sh

./configure

make

make check

sudo make install

- objectivec/DevTools/full_mac_build.sh

```
执行完 protobuf解压后的文件夹 src目录下生成了protoc二进制文件。


3.转换proto
转换需要在src目录执行，最好在src目录下新建两个文件夹，protocols源文件夹和output输出文件夹，将创建的xxx.proto放在protocols文件夹下，执行命令，如果有多个文件，一定要用空格分割。
```
// 格式
protoc --proto_path=protocols文件夹路径 --objc_out=输出文件夹路径 xxx1.proto xxx2.proto

protoc --proto_path=/Users/mni/Desktop/protobuf-3.12.1/src/protocols --objc_out=/Users/mni/Desktop/protobuf-3.12.1/src/output msg.proto conversation.proto
```

4. 集成到工程中所有的xxx.pbobjc.m 都是mrc的Complier Flags设为-fno-objc-arc

![WX20191115-213408@2x.png](https://upload-images.jianshu.io/upload_images/2051176-36b37c0912141024.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###三、IMUI编写

目前效果图：文本消息、图片消息、语言消息。根据数据是对方还是自己发送，动态适配UI。
![WX20191121-155443@2x.png](https://upload-images.jianshu.io/upload_images/2051176-f90e9b8114af7f13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![图片发自简书App](http://upload-images.jianshu.io/upload_images/2051176-70a9c0e57c41494e.jpg)

![图片发自简书App](http://upload-images.jianshu.io/upload_images/2051176-40a3daae5fe0581f.jpg)

