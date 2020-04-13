# Here Comes Your Flutter

## ListView

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

#### 已解决 这样就算点击没有颜色的地方也会触发onTap 2020/3/25

 ``` dart
 GestureDetector(
   behavior: HitTestBehavior.translucent,
    onTap: () async {
      Navigator.pop(context);
    },
    child: Container(
      alignment: Alignment.center,
      height: screenUtil.adaptive(120),
      child: Text('取消',
          style: TextStyle(
            fontSize: 16,
            color: Color(0xff828282),
          )),
    ),
 )
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

### `TextFormField` 的 `initialValue` 不更新问题 2020/3/25

最近在做一个输入框的时候发现: `initialValue` 值的只初始化一次. `TextFormField`初始化完成后 `initialValue` 的值如何改变都不会影响到 `TextFormField` 中输入的值.

如果输入框初始完成后还需要改变输入框中的值的话就需要用到 `TextEditController`.
将 Controller 给到 `TextFormField` 后修改 `TextEditController.text` 这时的改动就会同步到输入框里面.

[https://stackoverflow.com/questions/58053956/setstate-does-not-update-textformfield-when-use-initialvalue](参考)

### 如何在页面初始化数据的时候展示一个转圈圈(加载) 2020/3/25

在`initState`调用的时候`context`并没有加载好. 所以`initState`转圈的显示如果需要用到`context`的话就会报错.

处理办法是:

``` dart
@override
  void initState() {
    _fetchJobDetail();
    super.initState();
  }

  Future<void> _fetchJobDetail() async {
    Future.delayed(Duration.zero, () async { // 将转圈和请求数据转为一个异步操作 等待同步的context加载完成
      final res = await loadingCallback( // loadingCallback 的作用是在RecruitmentService.fetchJobDetail完成之前在页面上显示一个转圈
          () async => await RecruitmentService.fetchJobDetail(widget.id),
          context: context);

      setState(() {
        info = res;
        jobTitleController.text = res.jobTitle;
      });
    });
  }
```

将转圈和请求数据转为一个异步操作 等待同步的context加载完成

### 后端的JSON中快速简历一个模型类 2020/3/25

[JSON to Dart](https://javiercbk.github.io/json_to_dart/)

### Dio上传多张图片 2020/3/26

``` dart
  static Future<List> uploadReportImages(List<Asset> images) async {
    try {
      final List<Future<MultipartFile>> fuMutiPart = images.map((asset) async {
        ByteData byteData = await asset.getByteData();

        List<int> imageData = byteData.buffer.asUint8List();

        return MultipartFile.fromBytes(imageData,
            filename: asset.name,
            contentType: MediaType("image", "png"));
      }).toList();

      final List<MultipartFile> mutiPartList = await Future.wait(fuMutiPart);

      FormData form = FormData.fromMap({"file": mutiPartList});

      final result =
          await BaseService.http.post('recruitment/index/upload', data: form);

      if (result.data is DioError) {
        showToast(result.data.error);
        throw (result.data);
      }

      final List data = result.data['data'];

      if (result.data['code'] == 200 && data.isNotEmpty) {
        return data;
      }
    } catch (e) {
      debugPrint(e.toString());
    }
  }
```

Dio在上传文件数组的时候会给字符串Key加上一个`[]`所以Key就变成了`Key[]`. 在上面的示例代码中`FormData.fromMap({"file": mutiPartList})` 如果mutiPartList是一个List. Key会被Dio的`FormData.fromMap`转为`file[]`. 可能导致后端key不同接收不到数据.

[参考](https://github.com/flutterchina/dio/issues/10)

### TabBar 的 Padding

`Tab`默认有一个`EdgeInsets.symmetric(horizontal: 16)`的`Padding`. [ref](https://github.com/flutter/flutter/issues/21694#issue-359151684)
在如果`Tab`中的文字过长那么就会显示不全, 可以把默认的`Padding`设为0.
这个`Padding`非常神奇他不是设置在`Tab`上的. 是设置在`TabBar`的`labelPadding`上的. 估计Flutter团队想让每个`Tab.padding`都统一把.

**Come on baby light my fire**
**来吧宝贝点燃我的火焰**

### 七月四日的帝国大厦大小不过一枚硬币

### 迷幻藏于火焰中, 触碰的到感觉是温暖的丝绒味道

### 世界亦如蕾丝般柔软

**Stereolab成立30周年的首次中国巡演取消了.**
世界上再也没有实验电子乐了. Stereolab有一首歌叫Flutter, 这实在是太巧合了. 请大家主动上民政局网站网上祭祀.
中国国内的新乐队. 马勒戈壁的唱的都是什么鸡巴玩意. 赶紧解散回家种田去. 不我的意思是我觉得你们都是垃圾, 真正意义上的垃圾, 你们有那么多前辈就不会学一下吗? 写出来的歌都是什么狗屎. 台湾垃圾也的滚回台湾去, 哪里的垃圾哪里回收. 别来这恶心我.

油腻诗人横行, 文化贫瘠下请你忍辱负重任重道远保持愤怒.

墨西哥强盗四处横行, 赏金猎人紧随其后, 体制下的肥猪吃下了最后一勺恶臭的泔水, 滚烫的血液再也不流动了.

墙上的倒影永远不会再有变化, 除非光线经历了黎明到日落.

year 2000 是我出生的一年.

在那寂静四分五裂的地方, 还有人挖着洞.
