# 0706--网易新闻首页\UIScrollVIew布局

- 步骤总结和bug
- 1, 在viewdidload拿到的控件的尺寸是不准确的
- 2, 要在viewdidapear里边拿到的控件的尺寸是准确的
- 3, 要设置控制器的`    self.automaticallyAdjustsScrollViewInsets = NO;`要不然头部scrollView的下边会占用内容scrollview的头部
- 4, 同步滚动的时候, 要把头部scrollview的label放到中间, 但是头尾的label的要处理, 否则会滚动出界
- 5, 默认选中第一个内容控制器// 默认显示第0个子控制器, 并且设置第一个label的`scale为1`
   ` [self scrollViewDidEndScrollingAnimation:self.contentScrollView]`
- 6, 监听滚动, 拿到label的时候, index会越界, 当contentoffset.x<0的时候要return, 当index大于最大脚标的时候, 也要return,
- 7,穿透label的大小和颜色的残留处理, 在滚动动画结束后, 遍历头部scrollView的子控件, 设置scale为0
- 8,如果是手动拖拽内容的`ScrollView`, 需要手动调用`- (void)scrollViewDidEndScrollingAnimation:`

### 方法的调用与否的注意
- 用系统的方法`setContentOffset`滚动后会调用这个方法`scrollViewDidEndScrollingAnimation:`

```objc
/**
 * scrollView结束了滚动动画以后就会调用这个方法
 （比如- (void)setContentOffset:(CGPoint)contentOffset animated:(BOOL)animated;方法执行的动画完毕后）
 */
- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView;

```

- 手动拖拽结束后会调用下面这个方法`scrollViewDidEndDecelerating`

```objc

/**
 * 手指松开scrollView后，scrollView停止减速完毕就会调用这个
 */
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView
{
    [self scrollViewDidEndScrollingAnimation:scrollView];
}

```

- 而`scrollViewDidScroll:`是实时调用的

- 自定义label设置放大和缩小, 和字体颜色

```objc

- (void)setScale:(CGFloat)scale
{
    _scale = scale;
    //      R G B
    // 默认：0.4 0.6 0.7
    // 红色：1   0   0

    CGFloat red = XMGRed + (1 - XMGRed) * scale;
    CGFloat green = XMGGreen + (0 - XMGGreen) * scale;
    CGFloat blue = XMGBlue + (0 - XMGBlue) * scale;
    self.textColor = [UIColor colorWithRed:red green:green blue:blue alpha:1.0];

    // 大小缩放比例
    CGFloat transformScale = 1 + scale * 0.3; // [1, 1.3]
    self.transform = CGAffineTransformMakeScale(transformScale, transformScale);
}

```

## demo

```objc

@interface XMGHomeViewController () <UIScrollViewDelegate>
@property (weak, nonatomic) IBOutlet UIScrollView *titleScrollView;
@property (weak, nonatomic) IBOutlet UIScrollView *contentScrollView;
@end

@implementation XMGHomeViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    NSLog(@"XMGHomeViewController - %f %f %f", XMGRed, XMGGreen, XMGBlue);

    // 添加子控制器
    [self setupChildVc];

    // 添加标题
    [self setupTitle];

    // 默认显示第0个子控制器
    [self scrollViewDidEndScrollingAnimation:self.contentScrollView];
}

/**
 * setupChildVc
 */
- (void)setupChildVc
{


    XMGSocialViewController *social6 = [[XMGSocialViewController alloc] init];
    social6.title = @"娱乐";
    [self addChildViewController:social6];
}

/**
 * 添加标题
 */
- (void)setupTitle
{
    // 定义临时变量
    CGFloat labelW = 100;
    CGFloat labelY = 0;
    CGFloat labelH = self.titleScrollView.frame.size.height;

    // 添加label
    for (NSInteger i = 0; i<7; i++) {
        XMGHomeLabel *label = [[XMGHomeLabel alloc] init];
        label.text = [self.childViewControllers[i] title];
        CGFloat labelX = i * labelW;
        label.frame = CGRectMake(labelX, labelY, labelW, labelH);
        [label addGestureRecognizer:[[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(labelClick:)]];
        label.tag = i;
        [self.titleScrollView addSubview:label];

        if (i == 0) { // 最前面的label
            label.scale = 1.0;
        }
    }

    // 设置contentSize
    self.titleScrollView.contentSize = CGSizeMake(7 * labelW, 0);
    // 在这里边拿到的xib里边的view的尺寸是不准确的
    self.contentScrollView.contentSize = CGSizeMake(7 * [UIScreen mainScreen].bounds.size.width, 0);
}

/**
 * 监听顶部label点击
 */
- (void)labelClick:(UITapGestureRecognizer *)tap
{
    // 取出被点击label的索引
    NSInteger index = tap.view.tag;

    // 让底部的内容scrollView滚动到对应位置
    CGPoint offset = self.contentScrollView.contentOffset;
    offset.x = index * self.contentScrollView.frame.size.width;
    [self.contentScrollView setContentOffset:offset animated:YES];
}

#pragma mark - <UIScrollViewDelegate>
/**
 * scrollView结束了滚动动画以后就会调用这个方法（比如- (void)setContentOffset:(CGPoint)contentOffset animated:(BOOL)animated;方法执行的动画完毕后）
 */
- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView
{
    // 一些临时变量
    CGFloat width = scrollView.frame.size.width;
    CGFloat height = scrollView.frame.size.height;
    CGFloat offsetX = scrollView.contentOffset.x;

    // 当前位置需要显示的控制器的索引
    NSInteger index = offsetX / width;

    // 让对应的顶部标题居中显示
    XMGHomeLabel *label = self.titleScrollView.subviews[index];
    CGPoint titleOffset = self.titleScrollView.contentOffset;
    titleOffset.x = label.center.x - width * 0.5;

    // 左边超出处理
    if (titleOffset.x < 0) titleOffset.x = 0;
    // 右边超出处理
    CGFloat maxTitleOffsetX = self.titleScrollView.contentSize.width - width;
    if (titleOffset.x > maxTitleOffsetX) titleOffset.x = maxTitleOffsetX;

    [self.titleScrollView setContentOffset:titleOffset animated:YES];

    // 让其他label回到最初的状态
    for (XMGHomeLabel *otherLabel in self.titleScrollView.subviews) {
        if (otherLabel != label) otherLabel.scale = 0.0;
    }

    // 取出需要显示的控制器
    UIViewController *willShowVc = self.childViewControllers[index];

    // 如果当前位置的位置已经显示过了，就直接返回
    if ([willShowVc isViewLoaded]) return;

    // 添加控制器的view到contentScrollView中;
    willShowVc.view.frame = CGRectMake(offsetX, 0, width, height);
    [scrollView addSubview:willShowVc.view];
}

/**
 * 手指松开scrollView后，scrollView停止减速完毕就会调用这个
 */
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView
{
    [self scrollViewDidEndScrollingAnimation:scrollView];
}

/**
 * 只要scrollView在滚动，就会调用
 */
- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    CGFloat scale = scrollView.contentOffset.x / scrollView.frame.size.width;
    if (scale < 0 ) return;

    // 获得需要操作的左边label
    NSInteger leftIndex = scale;
    XMGHomeLabel *leftLabel = self.titleScrollView.subviews[leftIndex];

    // 获得需要操作的右边label
    NSInteger rightIndex = leftIndex + 1;
    if(rightIndex > self.titleScrollView.subviews.count - 1) return;
    XMGHomeLabel *rightLabel = self.titleScrollView.subviews[rightIndex];

    // 右边比例
    CGFloat rightScale = scale - leftIndex;
    // 左边比例
    CGFloat leftScale = 1 - rightScale;

    // 设置label的比例
    leftLabel.scale = leftScale;
    rightLabel.scale = rightScale;
}

@end

```

