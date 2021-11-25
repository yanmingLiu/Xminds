
1.showModalBottomSheet中Column+scrollView高度超过bottomsheet的滚动问题。

```
处理方法：
Flexible(
   child: ListView.builder(
       shrinkWrap: true,
   ))
```

2.自定义dialog点击显示区域也要触发透明层事件的问题。

```
处理方法：在显示区域再添加一个手势隔断透明层手势。
return GestureDetector(
      // 点击透明层pop
      onTap: _cancelCallback,
      child: Material(
        type: MaterialType.transparency,
        child: Center(
          // 过滤外层手势
          child: GestureDetector(
            onTap: () {},
```

3.flutter_cupertino_date_picker自定义title后，确定按钮回调不走原始回调，无法获取值的问题。

```
处理方法：onChange的回调中实时记录当前值
```

4.bottomsheet弹出输入框键盘遮挡问题。

```
处理方法：自定义PopupRoute 、已push页面的方式弹出


void showModalBottomDialog({
  @required BuildContext context,
  @required WidgetBuilder builder,
}) {
  Navigator.push(context, _PopRoute(builder: (context) {
    return Scaffold(
      backgroundColor: Colors.black54,
      body: GestureDetector(
        behavior: HitTestBehavior.opaque,
        onTap: () => Navigator.pop(context),
        child: Container(
          child: GestureDetector(
            onTap: (){},
            child: Align(
                alignment: Alignment.bottomCenter,
                child: Container(
                    margin:
                        EdgeInsets.only(top: MediaQuery.of(context).padding.top),
                    color: AppColors.backgroundGrey,
                    child: builder.call(context))),
          ),
        ),
      ),
    );
  }));
}

class _PopRoute extends PopupRoute {
  _PopRoute({this.builder});

  final WidgetBuilder builder;

  @override
  Color get barrierColor => null;

  @override
  bool get barrierDismissible => true;

  @override
  String get barrierLabel => null;

  @override
  Widget buildPage(BuildContext context, Animation<double> animation,
      Animation<double> secondaryAnimation) {
    return builder.call(context);
  }

  @override
  Duration get transitionDuration => Duration(milliseconds: 0);
}

```

5.FlutterViewController加载flutter页面, 导航栏返回按钮无效的问题。

```
处理方法：
flutter_boost插件每次open flutter页面的时候都要去获取当前栈顶控制器的navigator，
在app启动时候初始初始化PlatformRouterImp的时设置的navigator，
而iOS app结构中其实有多个navigator，
所以PlatformRouterImp open flutter页面中的navigationController，
并不一定是当前页面的栈顶导航栏，
所以出现无法push,或者pop的情况。
修改办法，在PlatformRouterImp open或者close的时候都实时去获取当前页面的导航控制器。
```
