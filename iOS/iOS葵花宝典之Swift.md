记录工作中使用过的第三方和优秀的框架, 感谢开源框架对我们开发者的帮助.
此文分为6个类目分别记录, 方便第一时间找到所需的第三方.

<!-- GFM-TOC -->
* [1. 网络篇](#1)
* [2. 解析篇](#2)
* [3. 缓存篇](#3)
* [4. 图片篇](#4)
* [5. 布局篇](#5) 
* [6. 工具篇](#6)
* [7. UI篇](#7)
<!-- GFM-TOC -->

<h2 id='1'>网络篇</h2>

**[Alamofire](https://github.com/Alamofire/Alamofire)** swift版本的AFNetworking , 都是matt大神作品.

**[Moya](https://github.com/Moya/Moya)** Moya是一个将Alamofire进行封装的网络抽象库. 由几个模块:
* provider 是一个提供网络请求服务的提供者
* Request  直接用provider来发起request.
* Response Response这个类对于请求结果, 提供了一些加工方法, 比如data转json, 图片转换等.
* Plugins Moya提供了一个插件协议PluginType, 协议里规定了几种方法, 阐明了插件的应用区域.

<h2 id='2'>解析篇</h2>

**[SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON)** SwiftyJSON 老牌的 JSON 解析库, 对于嵌套复杂的 JSON 数据依然能非常灵活地取值.

**[HandyJSON](https://github.com/alibaba/HandyJSON)** 阿里巴巴出品swift解析库, 上手容易 , 目前使用它.

**[KakaJSON](https://github.com/kakaopensource/KakaJSON)**  MJ老师出品.

<h2 id='3'>缓存篇</h2>

**[YYCache](https://github.com/ibireme/YYCache)** YYKit影响力相信国内iOS开发者无人不知.

**[WCDB](https://github.com/Tencent/wcdb/wiki#wcdb-for-iosmacos)** 腾讯微信出品, 这个就是理由. WCDB是一个高效、完整、易用的移动数据库框架, 支持iOS, macOS和Android.

**[Cache](https://github.com/hyperoslo/Cache)** 轻量级缓存框架. 它除了缓存什么都不做, 但它做得很好. 它提供了一个很好的公共 API, 具有开箱即用的实现和强大的定制可能性.

**[GRDB.swift](https://github.com/groue/GRDB.swift)** 与SQLite.swift或FMDB相比, GRDB可以省去很多胶水代码. 与Core Data或Realm相比, 它可以简化您的多线程应用程序

<h2 id='4'>图片篇</h2>

**[Kingfisher](https://github.com/onevcat/Kingfisher)** 喵神 onevcat 开源及维护. swift版的 SDWebImage.

**[ZLPhotoBrowser](https://github.com/longitachi/ZLPhotoBrowser)** 微信样式的图片选择器, 支持预览/相册内拍照及录视频、拖拽/滑动选择, 编辑图片/视频, 支持多语言国际化等功能.

**[Lantern](https://github.com/fcbox/Lantern)** 丰巢研发团队开发维护图片浏览框架.

<h2 id='5'>布局篇</h2>

**[SnapKit](https://github.com/SnapKit/SnapKit)** swift版的 Masonry.

<h2 id='6'>工具篇</h2>

**[fastlane](https://github.com/fastlane/fastlane)**  自动打包, 发布蒲公英, 上架app.

**[CryptoSwift](https://github.com/krzyzanowskim/CryptoSwift)** 非常流行的加密解密库, 项目中常用的加密解密都可以用这个库.

**[SwiftDate](https://github.com/malcommac/SwiftDate)** 非常好用的帮助处理 Date 相关的库. 灵活运用了 swift 的重载操作符、扩展等特性, 使得时间可以直观的进行算术运算: 比较大小, 直接加减等.

**[R.swift](https://github.com/mac-cain13/R.swift)** 优雅地引用项目资源, 图片、多语言文字、字体、颜色、xib等.

**[IQKeyboardManagerSwift](https://github.com/hackiftekhar/IQKeyboardManager)** 键盘管理

**[URLNavigator](https://github.com/devxoul/URLNavigator)** ⛵️URLNavigator提供了一种优雅的方式来通过URL在视图控制器之间进行导航. 可以使用URLNavigator.register(_:_:)功能映射URL模式.

<h2 id='7'>UI篇</h2>

**[ESPullToRefresh](https://github.com/eggswift/pull-to-refresh)** swift下拉刷新和加载更多组件, 但是Bug有点多.

**[MJRefresh](https://github.com/CoderMJLee/MJRefresh)** 即便是swift依然没有找到比它更好的.

**[FSPagerView](https://github.com/WenchaoD/FSPagerView)** 是一个优雅的轮播库, 主要通过UICollectionView实现.

**[MarqueeLabel](https://github.com/cbpowell/MarqueeLabel)** 跑马灯

**[CollectionViewWaterfallLayout](https://github.com/ecerney/CollectionViewWaterfallLayout)**
**[WaterfallMultiSectionFlowLayout](https://github.com/RoganZheng/WaterfallMultiSectionFlowLayout)**
瀑布流基于 UICollectionViewFlowLayout 实现, 支持多 section 场景下瀑布流、线性排列、九宫格样式.

**[ActiveLabel](https://github.com/optonaut/ActiveLabel.swift)** 处理标签中 # 符号, @ 符号, 以及超链接文本的框架. 它可以识别并标注 label 视图中的这些要素, 并为它们添加点击事件.

**[SwiftyAttributes](https://github.com/eddiekaiger/SwiftyAttributes)**   先进的 API , 操作 attributed 字符串的利器.

**[Toast-Swift](https://github.com/scalessec/Toast-Swift)**  使用简单弹窗提示.

**[NotificationBannerSwift](https://github.com/maheshbutani/NotificationBannerSwift-customizable-in-app-notification-)** 类似系统通知提示.

**[TextFieldEffects](https://github.com/raulriera/TextFieldEffects)**   各种动画效果输入框.

**[SkeletonView](https://github.com/Juanpe/SkeletonView)** 骨架屏, demo中collection cell未加载出子视图是因为demo中子视图是添加到cell上, 改为  contentView.addSubview(imageView)即可.

**[EmptyStateKit](https://github.com/alberdev/EmptyStateKit)** 空白页灵活性和易用性, 在 UITableView / UICollectionView中显示出色的占位符为空状态.

**[NVActivityIndicatorView](https://github.com/ninjaprox/NVActivityIndicatorView)** 32种loading加载动画.

**[FloatingPanel](https://github.com/SCENEE/FloatingPanel)** 评论弹窗浮窗, 高德地图首页浮窗效果.

**[MultiProgressView](https://github.com/mac-gallagher/MultiProgressView)** 手机存储空间一样的进度条.

**[JXPagingView](https://github.com/pujiaxin33/JXPagingView)**  个人主页效果, 多页面嵌套, 既可以上下滑动, 也可以左右滑动切换页面.

**[HSStockChart](https://github.com/zyphs21/HSStockChart)** K线图

**[vap](https://github.com/Tencent/vap)** 直播腾讯用于播放酷炫动画的实现方案.
