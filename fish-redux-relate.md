## Fish-Redux
update: 2020/4/29
说实话项目进行这么久了，许多页面都是使用*fish*来进行状态管理的。 
我个人感觉*fish*的代码太过于冗余，一个能由`StateLessWidget`实现的页面在*fish*中需要:
1. `page.dart`配置各各文件.
2. `state.dart`声明页面需要的数据.
3. `view.dart`构建界面.
而直接使用`StateLessWidget`一个`Class`就搞定实在是太爽了. 我个人不推荐使用*fish*.

### 1.fish-redux Page Slot注册Component
fish-redux中的Page.dart中的`Slot`可以在其中注册`Component`(fish-redux中的Component). 组件的目的是用于多次复用所以必须传递数据到`Component`中(如果遇到不需要传递数据的情况请直接使用`Page`). `Component`中的数据实际是存储在使用/调用他的`Page`中的。 根据fish-redux文档的描述`Component`是短暂的随时可能销毁，而`Page`是持久的，随时可能销毁的`Component`需要一个地方存储数据.

而传递数据必须要`Connector`来进行. `Connector`有两个方法需要被重写才能正确传递和存储数据:
1. `get`用于传递数据给`Component`.
2. `set`用于`Component`改变传递下来数据时如何同步到数据实际存储的地方.

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
                    viewService.buildComponent('banner'), // 与page.dart slots中注册的名字对应
                ]
            )
        )
    )
}
```

### extended_nested_scroll_view 的使用
-在(extended)NestedScrollView的body部分会在滚动到顶部时固定在屏幕顶端-


### 2. fish-redux 中使用 MutableSource Adapter 多源适配器

使用场景: Page中有一个List需要根据其中的数据来渲染不同的Component时用到(当然只渲染一个Component也是没有问题的).
``` dart
// adapter.dart
class BillsAdapter extends SourceFlowAdapter<BillsState> {
  BillsAdapter()
      : super(
          pool: <String, Component<Object>>{
            'item': BillsItemComponent(), // 账单component
            'month': BillMonthItemComponent() // 月账单组件component
            // 根据 state.dart getItemType 函数返回的字符串来选择相应的 Component
          },
        );
}

// state.dart
class BillsState extends MutableSource implements Cloneable<BillsState> { 
  // 注册多源适配器的Page/Component, state一定要继承 MutableSource 并覆盖四个方法 getItemData getItemType itemCount setItemData.
  
  List<dynamic> bills = []; // 需要被多个Component渲染的List 因为List里有多个 ComponentState 所以使用 List<dynamic> 作为类型
  
  @override
  BillsState clone() {
    return BillsState()..bills = bills;
  }

  @override
  Object getItemData(int index) {  // getItemData 负责将数据传入 getItemType 中返回的组件
    return bills[index];
  }

  @override
  String getItemType(int index) { 
    // 根据当前 index 拿出 bills 中对应的数据来进行判断 fish会根据返回的字符来渲染adapter.dart中对应的Component进行渲染
    if (bills[index].bill.billType == 'month') {
      return 'month';
    }

    return 'item';
  }

  @override
  int get itemCount => bills.length; // 需要渲染多个数据 一般是需要渲染的List的length

  @override
  void setItemData(int index, Object data) { 
    // Component中数据被修改后存储到哪里 Component是短暂的随时可能会被销毁 数据需要一个地方进行存储 这个地方就是Page
    bills[index] = data; 
  }
}

// page.dart
class BillsPage extends Page<BillsState, Map<String, dynamic>> {
  BillsPage()
      : super(
            initState: initState,
            effect: buildEffect(),
            reducer: buildReducer(),
            view: buildView,
            dependencies: Dependencies<BillsState>(
                adapter: NoneConn<BillsState>() + BillsAdapter(), // 在Page.dart中必须要在这将adapter注册到Page中 (才能在View中使用adapter)
                slots: <String, Dependent<BillsState>>{
                }),
            middleware: <Middleware<BillsState>>[
            ],);
}


// view.dart
Widget buildView(BillsState state, Dispatch dispatch, ViewService viewService) {
  final adapter = viewService.buildAdapter(); // build 适配器(adapter)
  return Scaffold(
    appBar: CustAppbar(
      title: Text(state.appBarTitle),
      centerTitle: true,
    ),
    body: ListView.builder( // 使用适配器渲染组件
      itemBuilder: adapter.itemBuilder,
      itemCount: adapter.itemCount,
    ),
  );
}
```

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

### 4. fish-redux View中的viewService.context使用要点

在View.dart中使用`viewService.context`作为Context来Push或者Pop页面时需要注意: 

1. `view.dart`中的`viewService.context`是*会*变化的. 如果页面中嵌套有TabBar, 在切换时会发生变化(? 没有考证过).
2. effct.dart中的被注册的effct函数中的`ctx.context`*不会*发生变化.
3. view.dart中的`viewService.context`会发生变化所以你在view中做Push Pop操作时的Context可能不是你期望的那个. 建议将路由操作全部都放在effct.dart中使用`dispatch(effct)`的方式来进行路由操作.

### 5. ListView.builder中嵌套Component

 `ListView.builder`中的元素都是按需加载的. 今天把`Adapter.itemBuilder`放在`ListView.builder`中出现了一个问题. 当父页面需要dispatch所有Component的一个Action时 部分Component没有被Builder出来, 所以没有接受到父页面中的dispatch. 我的解决方案是直接在父页面中修改子Component的数据, 不使用dispatch的让Component自己修改数据.
