## Here Comes Your Flutter

### ListView

跟`ListView.builder`比起来是会直接将所有子元素一次渲染完，`ListView.builder`是用户滚到时再渲染需要显示的子元素. 元素较多时使用builder将提高性能.

### ListView.builder

`ListView.builder`如果被装在一个flex容器中如`Columu` `Row`中.  那么需要给builder一个`shrinkWrap`属性或者给父元素一个滚动方向的长度限制.

```dart
Container(
   child: ListView.builder(
            itemCount: state?.waiting2Vote?.length, // 标明有多少元素需要渲染，未标明则会一直渲染， 数据没了就会报错.
            shrinkWrap: true, // 给Container一个height（默认垂直滚动）或者给builder一个这个属性
            itemBuilder: (BuildContext ctx, index) {
              if(state?.waiting2Vote != null) {
                return Vote_itemPage().buildPage(
                  (new Vote_itemState())
                    ..business =  state?.waiting2Vote[index].business
                    ..userLogoTag = state?.waiting2Vote[index].userLogoTag
                    ..isVo = '我当评委'
                    ..index = index
                );
              }
              return Text('');
    })
）
```

### Image Class

`Image.network` 加载一张网络图片必须给组件图片地址. 实例: `Image.file("http://ImageUrl")`.

`Image.file` 从ImageData中读取图片. 实例: `Image.file(ImageData.file)`.

`Image.asset` 从本地图片文件夹中读取一张图片. 实例: `Image.file("./Images/Imges.png")`.

### Flutter Bug(seems like a bug can you report for me?)

很奇怪的Bug

```dart
  GestureDetector(
              onTap: () async {
                Navigator.pop(context);
              },
              child: Container(
                color: Colors.indigo, // 如果这不给 `color` 属性的话 onTap 只会在点击 `取消` 两个字时触发. 给了 `color` 属性点击整个 Container 都触发 onTap
                alignment: Alignment.center,
                height: screenUtil.adaptive(120),
                child: Text('取消',
                    style: TextStyle(
                      fontSize: 16,
                      color: Color(0xff828282),
                    )),
              ),
            ),
 ```

### ListView.builder and Column/Row

`ListView.builder`在`Row`和`Column`中如果未添加主轴的限制的话就会导致RenderBox报错.

1. 使用 `shrinkWrap` 为 `True` 的 `ListView.builder` 会使用子元素高度(宽度)撑开 `ListView` 所以在 `Column` `Row` 中不会无限占据主轴宽度.
2. Expaned 会将 ListView 在 Column 中充满, 相当于给 ListView 设置了高度, 所以 ListView 在 Column 中的不会无线占据主轴高度.
``` dart
  Column(
    children: [
      Expaned( // 
        child: ListView.builder(itemBuilder: (..){})
      )
    ]
  )
```



### Refresh

实现一个 `refresh`

1. (https://www.youtube.com/watch?v=sI3UbjDcTsk)[Using the Refresh Indicator] 

2. (https://medium.com/@KarthikPonnam/flutter-loadmore-in-listview-23820612907d)[flutter loadmore in listview]

### Dart Stream

1.(https://zhuanlan.zhihu.com/p/55783611)[stream on dart and flutter(介绍)]


### 在 ListView.Builder 或 GridView.Builder 中在所有元素的头或尾部添加一个自定义元素

``` dart 
GridView(
                      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
                          crossAxisCount: 3, childAspectRatio: 1.3),
                      children: <Widget>[
                        SizedBox( // 自定义元素
                          width: screenUtil.adaptive(300),
                          height: screenUtil.adaptive(200),
                          child: Column(
                            crossAxisAlignment: CrossAxisAlignment.center,
                            children: <Widget>[
                              ImageHelper.asset(
                                'img/icon/add.png',
                                fit: BoxFit.fill,
                              ),
                              Text('相册')
                            ],
                          ),
                        ),
                        for (final i in state.images)
                          SizedBox( // 需要build的元素
                              width: screenUtil.adaptive(300),
                              height: screenUtil.adaptive(200),
                              child: Image.file(i.file))
                      ],
                    ),
```
1. 写代码时需要注意将重复的代码封装成方法, 反复使用.
2. 听取需求之后思考最简便的实现方案

### Map中的 await

List.map 回调函数中 `return await asyncThings..` 返回的不是await之后的数据是没有await的过的Future. 所以整个map返回的数组是List<Future>.
使用 `await Future.wait(List<Future>)` 将返回其中所有Future被await之后的数据.


### StatefulWidget 强制用户传入初始数据

``` dart
class Picker extends StatefulWidget {
  final double height;
  final ValueChanged<String> onTap;

  const EmojiPicker({
    @required this.height, // 这里添加@required
    @required this.onTap,
    Key key,
  })  : assert(height != null), // 并断言 height 不为 null, 为 null 时报错
        assert(onTap != null),
        super(key: key);

  @override
  _EmojiPickerState createState() => _EmojiPickerState();
}

```

### 将一个FofucsNode聚焦 (已经添加到TextField上)
``` dart
FocusScope.of(context).requestFocus(myFocusNode);
```


### android studio setting 
Select in project view `Alt + s`


### using base64 as image
``` dart
final res = base64.decode(base64String);

Container(
  width: screenUtil.adaptive(120),
  child: Image.memory(res),
)
```

### Stack set child fill
``` dart
Stack(
  children: <Widget>[
    Positioned.fill(
      child: background, // 这个子元素将充满Stack
    ),
    foreground,
  ],
);
```

### 七月四日的帝国大厦大小不过一枚硬币