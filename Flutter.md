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



