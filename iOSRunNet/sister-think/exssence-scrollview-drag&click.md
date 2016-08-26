# 精华控制器里边的标题栏的点击和下边scrollView的拖拽的联动

- 1, 先添加自控制器, 不显示自控制器的view, 并且告诉自控制器需要加载的帖子的类型, 和需要显示的标题

```objc
/**
 *  添加自控制器
 */
- (void)addChildViewControllers
{

    [self addChildViewController:[LMJTopicViewController topicViewControllerWithDataType:LMJTopicTypeVoice andTitle:@"声音"]];

    [self addChildViewController:[LMJTopicViewController topicViewControllerWithDataType:LMJTopicTypePicture andTitle:@"图片"]];

    [self addChildViewController:[LMJTopicViewController topicViewControllerWithDataType:LMJTopicTypeAll andTitle:@"全部"]];

    [self addChildViewController:[LMJTopicViewController topicViewControllerWithDataType:LMJTopicTypeVideo andTitle:@"视频"]];


    [self addChildViewController:[LMJTopicViewController topicViewControllerWithDataType:LMJTopicTypeWord andTitle:@"段子"]];
}
```

- 2, 添加标题栏, 标题栏的标题根据上边的自控制器的标题来设置
    - 设置标题栏的背景颜色和导航栏的颜色一样

```objc
- (void)setupTitlesView
{
    // 添加整体的view
    UIView *titlesView = [[UIView alloc] init];
    [self.view addSubview:titlesView];
    self.titlesView = titlesView;

    titlesView.x = 0;
    titlesView.y = CGRectGetMaxY(self.navigationController.navigationBar.frame);

    titlesView.width = self.view.width;
    titlesView.height = LMJTitlesViewInEssenceHeight;
    titlesView.backgroundColor = [UIColor colorWithPatternImage:[UIImage imageNamed:@"navigationbarBackgroundWhite"]];

    [self addButtonsInTitleswView:titlesView];

    [self addIndicatorViewInTitleswView:titlesView];
}

```

- 3, 在标题栏里边添加按钮和指示器, 并选中第一个按钮, 记录第一个按钮

```objc
// 选中了第一个
- (void)addButtonsInTitleswView:(UIView *)titlesView
{
    CGFloat buttonW = titlesView.width / self.childViewControllers.count;

    CGFloat buttonH = titlesView.height;

    CGFloat buttonX = 0;
    CGFloat buttonY = 0;

    for (NSInteger i = 0; i < self.childViewControllers.count; i++)
    {
        UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
        [titlesView addSubview:btn];
        btn.tag = i;

        [btn setTitle:self.childViewControllers[i].title forState:UIControlStateNormal];
        [btn setTitleColor:[UIColor grayColor] forState:UIControlStateNormal];
        [btn setTitleColor:[UIColor redColor] forState:UIControlStateDisabled];
        btn.titleLabel.font = [UIFont systemFontOfSize:15];

        buttonX = i * buttonW;
        btn.frame = CGRectMake(buttonX, buttonY, buttonW, buttonH);

        [btn addTarget:self action:@selector(selectTitle:) forControlEvents:UIControlEventTouchUpInside];

        if(i == 0)
        {
            [btn.titleLabel sizeToFit];
            self.selectedBtn = btn;
        }
    }
}

- (void)addIndicatorViewInTitleswView:(UIView *)titlesView
{
    UIView *indicatorView = [[UIView alloc] init];

    [titlesView addSubview:indicatorView];
    self.indicatorView = indicatorView;

    indicatorView.backgroundColor = [UIColor redColor];

    indicatorView.height = 2;
    indicatorView.y = titlesView.height - indicatorView.height;
}

```

- 4, 添加contentScrollView
    - 取消控制器的自动调整scrollView的内边距的属性
    - 设置代理监听scrollView的结束滚动, 设置值偏移量变化的时候的调用方法

```objc

- (void)addContentScrollView
{
    // 自己设置内边距
    self.automaticallyAdjustsScrollViewInsets = NO;

    UIScrollView *contentScrollView = [[UIScrollView alloc] initWithFrame:LMJMainScreen.bounds];
    [self.view insertSubview:contentScrollView atIndex:0];
    self.contentScrollView = contentScrollView;
    contentScrollView.delegate = self;

    // 设置contentScrollView的属性
    contentScrollView.contentSize = CGSizeMake(contentScrollView.width * self.childViewControllers.count, 0);
    contentScrollView.showsHorizontalScrollIndicator = NO;
    contentScrollView.pagingEnabled = YES;
}

```

- 5 , 点击按钮的时候, 找到按钮的索引, 根据索引设置scrollView的偏移量
    - 让指示器的位置和宽度也变化过去
    - 设置scrollView的contentOffset的时候, 如果没有发生改变, 就不会调用`scrollViewDidEndScrollingAnimation`
    -当手动拖拽scrollView停止的时候, 就会调用`scrollViewDidEndDecelerating`, 为了让指示器和title的里边的
        btn处于相应的位置和按钮的选中状态, 所以需要在这个方法中手动调用btn的点击事件方法, 和
        `scrollViewDidEndScrollingAnimation`方法
    - 在`scrollViewDidEndScrollingAnimation`方法中加载子控制器的view, 并设置frame
        - 如果view已经被添加就不需要再添加了

```objc
- (void)selectTitle:(UIButton *)btn
{
    self.selectedBtn.enabled = YES;
    btn.enabled = NO;
    self.selectedBtn = btn;

    // 指示器缓慢移动汇过去
    [UIView animateWithDuration:0.15 animations:^{
        self.indicatorView.width = self.selectedBtn.titleLabel.width;
        self.indicatorView.centerX = self.selectedBtn.centerX;
    }];

    CGPoint contentScrollViewContentOffset = self.contentScrollView.contentOffset;

    contentScrollViewContentOffset.x = self.selectedBtn.tag * self.contentScrollView.width;

    // 设置完后才会调用, 如果指没有变, 是不会调用的
    [self.contentScrollView setContentOffset:contentScrollViewContentOffset animated:YES];
}

- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView
{
    NSInteger index = scrollView.contentOffset.x / scrollView.width;

    UIViewController *vc = self.childViewControllers[index];

    // 如果已经加载到contentScrollView上边的时候, 就不用再加了
    if(vc.view.superview) return;

    vc.view.x = index * scrollView.width;

    [self.contentScrollView addSubview:vc.view];


}
/**
 *获得当前的索引,根据索引找到titlesview里边的button, 调用selectTitle:和scrollViewDidEndScrollingAnimation:
 */

- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView
{
    NSInteger index = 1.0 * scrollView.contentOffset.x / scrollView.width;

    [self selectTitle:[self.titlesView.subviews objectAtIndex:index]];

    [self scrollViewDidEndScrollingAnimation:scrollView];
}

```

- 6, 为了选中第一个控制器, 并且让scrollView里边的第一个控制器view加载, 需要调用

```objc
/**
 *  选中第一个btn后让指示器移动过去
 */

- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];

    self.indicatorView.width = self.selectedBtn.titleLabel.width;
    self.indicatorView.centerX = self.selectedBtn.centerX;
    // 间接调用了-scrollViewDidEndScrollingAnimation:
    [self scrollViewDidEndDecelerating:self.contentScrollView];
}

```

- 7, 如果把控制器的view添加到其他控制器的view里边, `子控制器view的frame.y默认是20`



