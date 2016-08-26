# add PictureView

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/0722-0807百思不得姐/图片帖子cell.png)

- 1, 在模型中增加服务器传递的基本图片数据, 同时增加pictureV的frame属性辅助

```objc
/** 图片的宽度 */
@property (nonatomic, assign) CGFloat width;
/** 图片的高度 */
@property (nonatomic, assign) CGFloat height;
/** 小图片的URL */
@property (nonatomic, copy) NSString *small_image;
/** 中图片的URL */
@property (nonatomic, copy) NSString *middle_image;
/** 大图片的URL */
@property (nonatomic, copy) NSString *large_image;
/** 帖子的类型 */
@property (nonatomic, assign) XMGTopicType type;

/****** 额外的辅助属性 ******/

/** cell的高度 */
@property (nonatomic, assign, readonly) CGFloat cellHeight;
/** 图片控件的frame */
@property (nonatomic, assign, readonly) CGRect pictureF;

```

- 2, 在模型的`.m`计算高度的时候, 加上图片的高度和需要的间距, 图片要按比例缩放, 并且设置最大的高度
    - 如果一个属性readonly, 在.m中只能`_`下划线访问, 但是会生成`_`的成员变量
    - 但是如果重写了这个属性的getter方法, 就不会生成下滑线的成员变量
    - 为了图片的通用型, 以后的图片可能是声音的图片啊, 视频的图片啊, 或者其他的图片,
        - 如果不是文字类型的帖子, 就需要图片, 所以计算了`所有图片`的尺寸, 并给pictureFrame设置
    - 里边如果不是文字后, 就是其他的, 但是, 有时候服务器返回的图片高度可能会出现0的情况,
    - 为了防止这种情况, 就要判断图片的高度服务器有木有返回
    - 图片的宽度和上边内容的label的最大宽度一样

```objc


- (CGFloat)cellHeight
{

    if(_cellHeight == 0)
    {
        // 先加到字体的Y第地方 + 内容距离底部条的间距+底部的条高度+cell内部减去的间距
        _cellHeight += LMJTopicCellTextLabelY + LMJTopicCellEdgeMargin + LMJTopicCellBottomBarHeight + LMJTopicCellEdgeMargin;

        // 计算里边   文字高度1+间距+图片2
//        CGFloat textLabelMaxWidth = LMJMainScreenWidth - 4 * LMJTopicCellEdgeMargin;

        CGFloat textLabelMaxWidth = LMJMainScreenWidth - 2 * LMJTopicCellEdgeMargin;

        CGFloat textLabelHeight = [self.text boundingRectWithSize:CGSizeMake(textLabelMaxWidth, MAXFLOAT) options:NSStringDrawingUsesLineFragmentOrigin attributes:@{NSFontAttributeName : [UIFont systemFontOfSize:14]} context:nil].size.height;

        // 加上文字的高度
        _cellHeight += textLabelHeight;

        /**如果不是文字cell, 就是其他的,
         */
//        self.height = 0;
        if(self.type != LMJTopicTypeWord && self.height != 0 && self.width != 0)
        {
#pragma mark - 如果不是文字=====================================================
            // 先把文字下边加上一个间距
            _cellHeight += LMJTopicCellEdgeMargin;

            /**
             *  无论是什么图片和声音和视频, 这几个都是固定的
             */
            CGFloat globalX = LMJTopicCellEdgeMargin;
            CGFloat globalY = LMJTopicCellTextLabelY + textLabelHeight + LMJTopicCellEdgeMargin;
            CGFloat globalW = textLabelMaxWidth;

#pragma mark  只用计算任何其他控件   的宽高和frame, 最后加上*******


                CGFloat pictureHeight = globalW * self.height / self.width;

            // 如果是图片, 就有可能过高, 其他控件设置frame的时候根本不会进来
                if(pictureHeight > LMJTopicCellPictureMaxHeight)
                {
                    pictureHeight = LMJTopicCellPictureBreakHeight;
                    _isBigPicture = YES;
                }

                // 计算图片的frame
                _pictureFrame = CGRectMake(globalX, globalY, globalW, pictureHeight);

                // 加上图片的高度
                _cellHeight += pictureHeight;

#pragma mark - 如果不是文字if结束===============================================
        }else
        {
            // 如果返回的图片的高度是0 , 就不计算, 并且设置type是文字类型
            self.type = LMJTopicTypeWord;
        }

    }

    return _cellHeight;
}


```

- 3, cell里边通过懒加载添加pictureView
    - 重写setTopic方法,在里边判断帖子的类型, 如果是图片帖子就直接设置图片的frame, 同时也就添加了图片控件

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160801_9.png)









