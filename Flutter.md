# Here Comes Your Flutter

## ListView

跟`ListView.builder`比起来是会直接将所有子元素一次渲染完，`ListView.builder`是用户滚到时再渲染需要显示的子元素. 元素较多时使用builder将提高性能.

**2020/4/23 update:**
`ListView.builder`如果`shrinkWrap`属性设置为`true`. 那么所有子元素就会一次渲染完. 效果类似直接使用`ListView`:

``` dart
class ListViewTest extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        body: SingleChildScrollView(
          child: Column(
            children: <Widget>[
              Container(
                color: Colors.redAccent,
                height: 200,
              ),
              ListView.builder(
                  shrinkWrap: true,
                  itemCount: 20,
                  physics: NeverScrollableScrollPhysics(),
                  itemBuilder: (_, int index) {
                    print('list 1: $index'); // 页面加载完成并没有进行任何滚动这里会直接log出 0-19(List长度已经超出屏幕).
                    return ListTile(
                        title: Text(
                          '$index',
                          style: titleStyle,
                        ));
                  }),
              Container(
                color: Colors.blue,
                height: 200,
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

这个可能是Flutter的一个bug, 按理来说就算加上了`shrinkWrap: true`也应该按需加载. [Issues在这里](https://github.com/flutter/flutter/issues/26072)
这个问题可以使用`CustomScrollView`解决. 将`ListView.Builder`替换成`SliverList`版本的`SliverChildBuilderDelegate`就可以解决:

``` dart
class ListViewTest extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        body: CustomScrollView(
          slivers: <Widget>[
            SliverToBoxAdapter(
              child: Container(
                color: Colors.redAccent,
                height: 200,
              ),
            ),
            SliverList(
              delegate: SliverChildBuilderDelegate((_, int index) {
                print('list: $index'); // 现在就不会在页面加载完后一次性加载所有子元素了
                return ListTile(
                    title: Text(
                  '$index',
                  style: titleStyle,
                ));
              }, childCount: 20),
            ),
            SliverToBoxAdapter(
              child: Container(
                color: Colors.blue,
                height: 200,
              ),
            )
          ],
        ),
      ),
    );
  }
}
```

## Image Class

`Image.network` 加载一张网络图片必须给组件图片地址. 实例: `Image.file("http://ImageUrl")`.

`Image.file` 从ImageData中读取图片. 实例: `Image.file(ImageData.file)`.

`Image.asset` 从本地图片文件夹中读取一张图片. 实例: `Image.file("./Images/Imges.png")`.

## Flutter Bug(seems like a bug can you report for me?)

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

**update 2020/3/25**
添加`behavior: HitTestBehavior.translucent`之后点击`GestureDetector`任何地方都会触发`onTap`.

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

## ListView.builder and Column/Row

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

## 将一个FofucsNode聚焦 (已经添加到TextField上)

``` dart
FocusScope.of(context).requestFocus(myFocusNode);
```

## android studio setting

Select in project view `Alt + s`

## using base64 as image

``` dart
final res = base64.decode(base64String);

Container(
  width: screenUtil.adaptive(120),
  child: Image.memory(res),
)
```

## `TextFormField` 的 `initialValue` 不更新问题 2020/3/25

最近在做一个输入框的时候发现: `initialValue` 值的只初始化一次. `TextFormField`初始化完成后 `initialValue` 的值如何改变都不会影响到 `TextFormField` 中输入的值.

如果输入框初始完成后还需要改变输入框中的值的话就需要用到 `TextEditController`.
将 Controller 给到 `TextFormField` 后修改 `TextEditController.text` 这时的改动就会同步到输入框里面.

update: 2020/8/27
 也可以使用

[参考](https://stackoverflow.com/questions/58053956/setstate-does-not-update-textformfield-when-use-initialvalue)

## 如何在页面初始化数据的时候展示一个转圈圈(加载) 2020/3/25

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

update: 2020/4/29:
发现了一个新的方法`addPostFrameCallback`,效果类似`Future.delay(Dutarion.zero, callback)`.

``` dart
import 'package:flutter/scheduler.dart';
@override
void initState() {
  super.initState();
  SchedulerBinding.instance.addPostFrameCallback((_) => {});
}
```

Medium大佬说的是这个函数的callback会在所有Widget加载完成后调用并且只调用一次而且必须要在`initState`中调用(This is a callback for the end of the frame, it only gets called once and we know for sure that the Widget build is completed). [ref](https://medium.com/flutter-community/flutter-lifecycle-for-android-and-ios-developers-8f532307e0c7)

## 后端的JSON中快速建立一个模型类 2020/3/25

[JSON to Dart](https://javiercbk.github.io/json_to_dart/)

## Dio上传多张图片 2020/3/26

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

      FormData form = FormData.fromMap({"file": mutiPartList}); // 如果mutiPartList的类型是List<MultipartFile>这里传递给后端的时候这个Key会被Dio给修改掉: file => file[]

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

Dio在上传文件数组的时候会给后端的字符串key加上一个`[]`所以Key就变成了`Key[]`.

在上面的示例代码中`FormData.fromMap({"file": mutiPartList})`如果mutiPartList是一个List, Key会被Dio的`FormData.fromMap`转为`file[]`, 数据传递到后端的时候需要用`"file[]"`来获取数据. [参考](https://github.com/flutterchina/dio/issues/10)

## TabBar 的 Padding

`Tab`默认有一个`EdgeInsets.symmetric(horizontal: 16)`的`Padding`. [ref](https://github.com/flutter/flutter/issues/21694#issue-359151684)
在如果`Tab`中的文字过长那么就会显示不全, 可以把默认的`Padding`设为0.
这个`Padding`非常神奇他不是设置在`Tab`上的. 是设置在`TabBar`的`labelPadding`上的. 估计Flutter团队想让每个`Tab.padding`都统一把.

## video_player 无法加载视频

使用`video_player: ^0.10.10`时无法加载视频, 错误信息如下:

``` bash
E/ExoPlayerImplInternal( 5671): Source error.
E/ExoPlayerImplInternal( 5671): com.google.android.exoplayer2.upstream.HttpDataSource$HttpDataSourceException: Unable to connect to http://test.bombox.org/test.mp4
E/ExoPlayerImplInternal( 5671):  at com.google.android.exoplayer2.upstream.DefaultHttpDataSource.open(DefaultHttpDataSource.java:281)
E/ExoPlayerImplInternal( 5671):  at com.google.android.exoplayer2.upstream.StatsDataSource.open(StatsDataSource.java:83)
E/ExoPlayerImplInternal( 5671):  at com.google.android.exoplayer2.source.ExtractorMediaPeriod$ExtractingLoadable.load(ExtractorMediaPeriod.java:885)
E/ExoPlayerImplInternal( 5671):  at com.google.android.exoplayer2.upstream.Loader$LoadTask.run(Loader.java:381)
E/ExoPlayerImplInternal( 5671):  at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1167)
E/ExoPlayerImplInternal( 5671):  at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:641)
E/ExoPlayerImplInternal( 5671):  at java.lang.Thread.run(Thread.java:764)
E/ExoPlayerImplInternal( 5671): Caused by: java.io.IOException: Cleartext HTTP traffic to test.bombox.org not permitted
E/ExoPlayerImplInternal( 5671):  at com.android.okhttp.HttpHandler$CleartextURLFilter.checkURLPermitted(HttpHandler.java:115)
E/ExoPlayerImplInternal( 5671):  at com.android.okhttp.internal.huc.HttpURLConnectionImpl.execute(HttpURLConnectionImpl.java:458)
E/ExoPlayerImplInternal( 5671):  at com.android.okhttp.internal.huc.HttpURLConnectionImpl.connect(HttpURLConnectionImpl.java:127)
E/ExoPlayerImplInternal( 5671):  at com.google.android.exoplayer2.upstream.DefaultHttpDataSource.makeConnection(DefaultHttpDataSource.java:528)
E/ExoPlayerImplInternal( 5671):  at com.google.android.exoplayer2.upstream.DefaultHttpDataSource.makeConnection(DefaultHttpDataSource.java:444)
E/ExoPlayerImplInternal( 5671):  at com.google.android.exoplayer2.upstream.DefaultHttpDataSource.open(DefaultHttpDataSource.java:279)
E/ExoPlayerImplInternal( 5671):  ... 6 more
V/DartMessenger( 5671): Sending message with callback over channel 'flutter.io/videoPlayer/videoEvents0'
I/flutter ( 5671): Video player had error com.google.android.exoplayer2.ExoPlaybackException: com.google.android.exoplayer2.upstream.HttpDataSource$HttpDataSourceException: Unable to connect to http://test.bombox.org/test.mp4
```

Google到一个解决方案[ref](https://github.com/flutter/flutter/issues/25749#issuecomment-535020528).
在`[ProjectRoot]/android/app/src/main/AndroidManifest.xml`有一个`<application>`标签给他加一个属性`android:usesCleartextTraffic="true"`.
一切就ok了.

## Ios上架

最近在弄公司App上架到app store的事情。为了证明我弄过ios上架流程，以及往后的无限幻想，这篇文章就诞生了。

### 当你的Flutter在启动的时候卡在了`Initializing gradle…`或`Running Gradle task 'assembleDebug'`请参考这篇[文章](https://blog.csdn.net/android157/article/details/103657486)
flutter启动时经常回去偷偷摸摸的去外网(世界上最大的局域网之外)下点东西下来, 启动时慢的大部分原因时因为网速慢, 东西下不下来. 这里写几个常用的镜像国内镜像, 设置环境变量后就可以使用:

 - 清华大学dart-pub镜像: `https://mirrors.tuna.tsinghua.edu.cn/dart-pub` (安装pubspec.yaml中声明的第三方包时用到[使用帮助页面](https://mirrors.tuna.tsinghua.edu.cn/help/flutter/))
 - 清华大学flutter-storage: `https://mirrors.tuna.tsinghua.edu.cn/flutter` (安装/更新fultter以及ios开发工具时用到[帮助页面](https://mirrors.tuna.tsinghua.edu.cn/help/flutter/))(在南方访问速度有点堪忧不过还是比flutterchina提供的镜像的快)
 - 上海交大dart-pub镜像: `https://dart-pub.mirrors.sjtug.sjtu.edu.cn` (速度在南方使用貌似会快很多, 但是有时候很不稳定, 装包装不下来只能改回清华pub镜像)
 - 上海交大flutter-storage: `https://mirrors.sjtug.sjtu.edu.cn` (速度快, 但是不稳定)
 - 清华Cocoapods镜像: `https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git` (ios在运行之前需要将pubspec.yaml中声明的一些包安装Swift版本需要使用到Cocoapods[帮助页面](https://mirrors.tuna.tsinghua.edu.cn/help/CocoaPods/))

目前我经常会在开发中用到的镜像就是这两个了. 清华镜像用起来比交大的要舒适一些, 有详细的介绍改如何使用稳定性挺不错的, 但就是有时候不知道啥原因速度慢(南方). 

如何解决开发时或者进行Build release时卡住:
1. (任何卡在`Initializing gradle…`或`Running Gradle task 'assembleDebug'`情况都适用).
2. 在IDE中运行`flutter Build`以及`flutter run`操作默认是不加`--verbose`参数的，大多数时候看不到需要的信息。如果没有足够的信息来侦错就可以手动去命令行中运行命令然后加上`--verbose`参数。
3. `--verbose` 参数. 这个参数的可以让Flutter运行的时候打印出所有的log(不加就不显示), 多数原因因为是网络问题下载(下载开发工具)无法完成卡住, 给了参数之后在进行下载之前Flutter一般会打印出去哪里下载了. 这时就可以根据log来修改环境变量解决下载不能的问题，修改环境变量可以参考标题里提供的地址以及，tuna镜像提供的帮助页面.
4. Windows下可以使用[proxifter](https://www.proxifier.com/)来代理命令行程序，Mac下可以参考这篇[文章](https://github.com/mrdulin/blog/issues/18)来给命令行走代理.

### `WillPopScope` In IOS
最近发现如果使用`WillPopScope`套在需要进行`Pop`操作的`Widget`(通常页面下这个`Widget`都是`Scaffold`)的外层在IOS上会导致划屏后退操作无法触发. 目前还没有测试Android手机上的划屏后退操作是否有效.
有关这个问题的讨论链接：[Remi](https://stackoverflow.com/a/49162131)

### App更新相关


更新需要后端服务器的支持,来根据不同的平台获取最新版本的App版本号后跟当前已经安装的版本进行对比,如果不同就需要提示升级. 
检查后发现版本后不一致时就需要提示升级, 两个主要手机平台升级方式不同: 
- Android
1. 直接下载新版Apk后安装更新.
2. 跳转到应用商店后用户安装更新.
   
- IOS
1. 跳转到App Store应用界面用户点击更新.

公司Android端更新主要是直接下载Apk来进行更新的, 如果需要跳转到应用市场进行更新的话就和IOS更新类似,直接复制应用市场地址然后进行跳转.

开发使用到的开源库:
1.  [PackgeInfo ^0.4.0+5](https://pub.dev/packages/package_info)专门用于检查当前安装的App版本号.
2.  [permission_handler ^3.2.2](https://pub.dev/packages/permission_handler)检查是否有权限进行安装Apk文件.
3.  [Dio](https://pub.dev/packages/dio)网络请求库，用于下载Apk文件.

``` java

// upgrade.dart

class NewVersionApkDialog extends StatefulWidget {

  final AppVersion version; // 服务器获取的最新版本号信息

  NewVersionApkDialog(this.version);

  @override
  _NewVersionApkDialogState createState() => _NewVersionApkDialogState();
}

class _NewVersionApkDialogState extends State<NewVersionApkDialog> {

  double progress = 0.0; // 下载进度

  bool isDownloading = false; // 是否在下载中

  bool isDownloadedNewVersionApk = false; // 是否已经下载了新版本Apk

  @override
  void initState() {
    checkIsDownloadedApk();
    super.initState();
  }

  Future<void> checkIsDownloadedApk() async {
    // 本地存储检查新版本Apk是否已经下载
    final downloadedVersion =
        await Application.sharePreference.get('DownloadedApkVersion');

    setState(() {
      // 检查从服务器获取的最新版本Apk是否是下载过了
      isDownloadedNewVersionApk =
          downloadedVersion == '${widget.version.id}+${widget.version.number}';
    });
  }

  Future<void> _downloadApk(String apkAddress) async {
    if (!isDownloading) {
      setState(() {
        isDownloading = true;
      });

      DownloadFile.download(apkAddress,
          progress: showDownloadProgress,
          whereSaveFile: () async => File(
              '${await DownloadFile.apkLocalPath}/new_version_xbd_apk_${widget.version.id}_${widget.version.number}.apk'),
          afterSave: () {
            setState(() {
              isDownloadedNewVersionApk = true;

              Application.sharePreference.putString('DownloadedApkVersion',
                  '${widget.version.id}+${widget.version.number}'); // 本地存储保存已经下载的Apk的版本号
            });
          },
          finalFunction: () {
            setState(() {
              isDownloading = false;
            });
          });
    }
  }

  void showDownloadProgress(received, total) {
    if (total != -1) {
      print((received / total * 100).toStringAsFixed(2) + "%");

      setState(() {
        progress = received / total;
      });
    }
  }

  Future<Null> _installApk(String apkFilePath) async {
    if (apkFilePath.isEmpty) {
      print('make sure the apk file is set');
      return;
    }

    // 安装Apk需要存储权限 先确保拥有权限

    Map<PermissionGroup, PermissionStatus> permissions =
    await PermissionHandler().requestPermissions([PermissionGroup.storage]);
    if (permissions[PermissionGroup.storage] == PermissionStatus.granted) {

      // 开源安装库专门用于安装Apk或者进行跳转到应用商店 
      InstallPlugin.installApk(apkFilePath, 'com.chunqiushiji.xbdapp')
          .then((result) {
        debugPrint('安装新版本结果 $result');
      }).catchError((error) {
        debugPrint('安装出错: $error');
      });
    } else {
      print('没有权限进行安装');
    }
  }

  void _appStore(String url) {
    // 填写App Store商店地址后在IOS上会直接跳转进入App Store页面进行更新
    InstallPlugin.gotoAppStore(url);
  }

  Widget _showUpgradeDialog() {
    TextStyle contentStyle =
        TextStyle(fontSize: getSp(15), fontFamily: fontMedium);

    TextStyle titleStyle = TextStyle(fontSize: getSp(18), fontFamily: fontBold);

    List<Widget> widgets = [];

    Widget subColumn = Padding(
      padding: EdgeInsets.symmetric(horizontal: gw(15)),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.stretch,
        mainAxisSize: MainAxisSize.min,
        children: <Widget>[
          Padding(
            padding: EdgeInsets.symmetric(vertical: 12),
            child: Text(
              '发现新版本',
              textAlign: TextAlign.center,
              style: titleStyle,
            ),
          ),
          Text.rich(TextSpan(text: '最新版本: ', style: titleStyle, children: [
            TextSpan(text: '${widget.version.id}', style: contentStyle)
          ])),
          Text(
            '新版本特性:',
            style: titleStyle,
          )
        ]..addAll(widget.version.content
            .asMap()
            .keys
            .map<Widget>((index) => Text(
                  '${index + 1}.${widget.version.content[index]}',
                  style:
                      TextStyle(fontSize: getSp(15), fontFamily: fontRegular),
                ))
            .toList()),
      ),
    );

    widgets.add(subColumn);

    if (isDownloading) {
      widgets.add(Center(
        child: Stack(
          alignment: Alignment.center,
          children: <Widget>[
            Container(
              margin: EdgeInsets.symmetric(horizontal: gw(15), vertical: 12),
              width: double.infinity,
              height: 30,
              child: LinearProgressIndicator(
                backgroundColor: Colors.grey,
                valueColor: AlwaysStoppedAnimation<Color>(mainColor),
                value: progress,
              ),
            ),
            Text(
              '${(progress * 100).toStringAsFixed(2)}%',
              style: TextStyle(
                  fontSize: getSp(12),
                  fontFamily: fontRegular,
                  color: Colors.white),
            )
          ],
        ),
      ));
    }

    if (widget.version.forced) {
      widgets.add(Padding(
        padding: EdgeInsets.symmetric(horizontal: gw(15))
            .add(EdgeInsets.only(top: 4)),
        child: Text(
          '本次更新涉及重要内容，需更新后使用',
          style: TextStyle(fontSize: getSp(15), fontFamily: fontBold),
        ),
      ));
    }

    if (!isDownloading && !isDownloadedNewVersionApk && Platform.isAndroid) {
      widgets.add(SubmitButton(
        onTap: () async {
          final hadPermission = await checkStoragePermission();

          if (hadPermission) {
            _downloadApk(widget.version.url);
          } else {
            showToast('未授权下载新安装文件');
          }
        },
        text: '立即更新',
      ));
    }

    if (isDownloadedNewVersionApk || Platform.isIOS) {
      widgets.add(SubmitButton(
        onTap: () async {
          if (Platform.isIOS && widget.version.type == 'ipa') {
            _appStore(widget.version.url);
          } else {
            _installApk(
                '${await DownloadFile.apkLocalPath}/new_version_xbd_apk_${widget.version.id}_${widget.version.number}.apk');
          }
        },
        text: '立即安装',
      ));
    }

    return GestureDetector(
      behavior: HitTestBehavior.translucent,
      onTap: () {},
      child: SingleChildScrollView(
        child: Container(
          padding: EdgeInsets.symmetric(vertical: 8),
          color: Colors.white,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.stretch,
            mainAxisSize: MainAxisSize.min,
            children: widgets,
          ),
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.transparent,
      body: GestureDetector(
        behavior: HitTestBehavior.translucent,
        onTap: () {
          if (!widget.version.forced && !isDownloading) {
            Navigator.pop(context);
          }
        },
        child: Container(
            padding: EdgeInsets.symmetric(horizontal: gw(15)),
            alignment: Alignment.center,
            height: double.infinity,
            width: double.infinity,
            color: Colors.black.withOpacity(.4),
            child: _showUpgradeDialog()),
      ),
    );
  }
}

// downloadFile.dart (DownloadFile.download方法在这里实现)

class DownloadFile {
  static Future<String> get apkLocalPath async {
    final directory = await getExternalStorageDirectory();
    return directory.path;
  }

  static Future<bool> download(String fileUrl,
      {void Function(int count, int total) progress,
      @required Future<File> Function() whereSaveFile,
      Function afterSave,
      Function finalFunction}) async {
    try {
      Response response = await Dio().get(
        fileUrl,
        onReceiveProgress: progress,
        options: Options(
            responseType: ResponseType.bytes,
            followRedirects: false,
            validateStatus: (status) {
              return status < 500;
            }),
      );

      File file = await whereSaveFile();
      var raf = file.openSync(mode: FileMode.write);
      raf.writeFromSync(response.data);
      await raf.close();

      if(afterSave is Function) {
        afterSave();
      }

      return true;
    } catch (e) {
      debugPrint('下载$fileUrl: ${e.toString()}');
      return false;
    } finally {
      if(finalFunction is Function) {
        finalFunction();
      }
    }
  }
}


```

### 如何给Flutter apk添加混淆
[link](https://medium.com/@swav.kulinski/flutter-and-android-obfuscation-8768ac544421)

  