## Here Comes Your Flutter

### ListView

跟`ListView.builder`比起来是会直接将所有子元素一次渲染完，`ListView.builder`是用户滚到时再渲染需要显示的子元素. 元素较多时使用builder将提高性能.

### ListView.builder

`ListView.builder`如果被装在一个flex容器中如`Columu` `Row`中.  那么需要给builder一个`shrinkWrap`属性或者给父元素一个滚动方向的长度限制.

```
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

```
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
dart ```

### ListView.builder and Column/Row

1. 使用 `shrinkWrpa` 为 `True` 的 `ListView.builder` 会使用子元素高度(宽度)撑开 `ListView` 所以在 `Column` `Row` 中不会无限占据主轴宽度.
2. ```
Column(
  children: [
    Expaned( // 
      child: ListView.builder(itemBuilder: (..){})
    )
  ]
)
```
Expaned 会将 ListView 的在 Column 中充满, 相当于给 ListView 设置了高度, 所以 ListView 在 Column 中的不会无线占据主轴高度.






