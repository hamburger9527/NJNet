# 导航栏和scrollView的注意点

- 导航栏默认会把第一个scrollView的`顶部内间距`增加64 = 20 + 44;
- 但是第二个不会增加

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160627_6.png)

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160627_7.png)

- 不会增大scrollView的尺寸, 只会设置scrollView:`contentInset = UIEdgeInsetsMake(64, 0, 0, 0)`

- 如果`scrollView`不是第一个添加的控件, 就不会设置

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160627_8.png)


- 如果不想让导航条自动设置`scrollView`的内边距

```objc

    不会自动去调整uiscrollView的contentInset属性
   self.automaticallyAdjustsScrollViewInsets = NO;

```

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160627_10.png)

