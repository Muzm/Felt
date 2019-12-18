## 最近工作中使用到的Flutter相关

### 1.fish-redux Page Slot注册Component
fish-redux中的Page.dart中的`Slot`可以在其中注册`Component`(fish-redux中的Component). 组件的目的是用于多次复用所以必须传递数据到`Component`中(如果遇到不需要传递数据的情况请直接使用`Page`). 而传递数据必须要`Connector`来进行.
在`Connector`中定义好`get`用于传递数据给`Component`, `set`用于`Component`改变传递下来数据时如何更新到`Page`.
``` dart
// page.dart
import 'connect.dart';
class HomePage extends Page<HomeState, Map<String, dynamic>> {
  HomePage()
      : super(
          initState: initState,
          effect: buildEffect(),
          reducer: buildReducer(),
          view: buildView,
          dependencies: Dependencies<HomeState>(
              adapter: NoneConn<HomeState>() + HomeListAdapter(),
              slots: <String, Dependent<HomeState>>{
                'banner': HomeBannerConnector() + BannerComponent(),
              }),
          middleware: <Middleware<HomeState>>[],
        );

// connect.dart
class HomeBannerConnector extends ConnOp<HomeState, BannerState> {
  @override
  BannerState get(HomeState state) { // 将state中的bannerState传递给component
    return state.bannerState; 
  }

  @override
  void set(HomeState state, BannerState subState) { // component中如果修改了bannerState将其同步
    state.BannerState = subState;
  }
}

// view.dart
Widget buildView(HomeState state, Dispatch dispatch, ViewService viewService) {
    return Scaffold(
        body: Container(
            child: Column(
                children: <Widget>[
                    viewService.buildComponent('banner'), // 与Slot中注册的名字相同
                ]
            )
        )
    )
}

}
```

### 2. fish-redux 中使用 MutableSource Adapter 多源适配器

使用场景: Page中有一个List需要被...

### 4.


