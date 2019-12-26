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

### extended_nested_scroll_view 的使用
在(extended)NestedScrollView的body部分除了NestedScrollViewInnerScrollPositionKeyWidget的子元素其他的元素都会被固定到顶部

### 2. fish-redux 中使用 MutableSource Adapter 多源适配器

使用场景: Page中有一个List需要被...

### 3. fish-redux 的 View 什么时候会更新
fish-redux中创建的reducer函数都需要返回一个对象. 默认更新条件是返回的对象是一个新对象并且是同类对象时(同一个类实例化的对象)fish-redux会去触发View的更新(如果只需要修改数据不更新View 就可以将State修改后直接返回). 除此之外在Page/Component中添加`shouldUpdate`函数用于控制页面是否更新, 函数在每次调用Reducer之后都会触发一次. `shouldUpdate`拥有两个参数分别是oldState和newState, oldState是调用Reducer之前的state, newState是Reducer返回的state. **如果`shouldUpdate`函数返回`true`那么View就会更新`false`不更新**

``` dart
// reducer.dart
MallItemDetailState _modifyCount(MallItemDetailState state, Action action) {
  state.count = 100;
  
  return state; // 默认不触发更新, 因为返回值不是一个新的[MallItemDetailState]对象
}

MallItemDetailState _selectCurrentStyle(MallItemDetailState state, Action action) {
  final MallItemDetailState newState = state.clone();
  newState.currentStyleIndex = action.payload;
  return newState; // 触发默认更新条件, 返回值是一个新的[MallItemDetailState]对象
}
// page.dart
class MallItemDetailPage extends Page<MallItemDetailState, Map<String, dynamic>> {
  MallItemDetailPage()
      : super(
            initState: initState,
            effect: buildEffect(),
            reducer: buildReducer(),
            view: buildView,
            shouldUpdate: (MallItemDetailState oldState, MallItemDetailState newState) => true, 
            /*
            返回true时reducer返回的是不是新对象都更新View,
            可以自行添加更新的条件在这里
            */
            dependencies: Dependencies<MallItemDetailState>(
                adapter: null,
                slots: <String, Dependent<MallItemDetailState>>{
                  'banner': MallItemDetailBannerConnector() + BannerComponent(),
                }),
            middleware: <Middleware<MallItemDetailState>>[
            ],);

}

```
### 4. Fish-redux View中的viewService.context使用要点
在View.dart中使用`viewService.context`作为Context来Push或者Pop页面时需要注意: 
1. `viewService.context`是*会*变化的. 如果页面中嵌套有TabBar, 在切换时会发生变化(? 没有考证过).
2. effct.dart中的被注册的effct函数中的`viewService.context`*不会*发生变化.
3. view.dart中的`viewService.context`会发生变化所以你在view中做Push Pop操作时的Context可能不是你期望的那个. 建议将路由操作全部都放在effct.dart中使用dispatch effct的方式来进行路由操作.


