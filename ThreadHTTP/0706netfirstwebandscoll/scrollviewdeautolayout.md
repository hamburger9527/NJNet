
 ## UIScrollView内部子控件添加约束的注意点：

 - 子控件的尺寸`不能通过UIScrollView`来计算，可以考虑通过以下方式计算
    - 可以设置`固定值`（width==100，height==300）
    - 可以相对于`UIScrollView以外的其他控件`来计算尺寸

 - UIScrollView的`frame`应该通过`子控件以外的其他控件`来计算
 - UIScrollView的`contentSize`通过子控件来计算
    - 根据`子控件的尺寸`以及`子控件与UIScrollView之间的间距`

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160628_7.png)
