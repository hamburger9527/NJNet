# scrollView内部控件悬停

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160628_1.png)

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160628_2.png)

- 在修改y的时候, 添加到父控件的时候会有bug, 原因控制器veiw的自动布局

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160628_3.png)

- 当放大iamgeView的时候会遮盖住下边的蓝色的View
    - 按照控件的添加顺序, 在蓝色的view下边, `最后`添加一个`空白的view`,
        - 再把蓝色的view添加到空白的`view里边`;
