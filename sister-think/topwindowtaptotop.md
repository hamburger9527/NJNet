# 在statusBar上边添加一个透明的alert窗口

- 通过点击窗口, 找到显示在当前窗口的tableview, 让起滚动到顶部
- 如果实现了盖住statusBar, 那么应用的statusBar就不显示了
- 要修改infoplist里边, 把状态栏的状态交给UIApplication管理

- 1, 里边用到递归
- 2, 设置窗口的优先级
- 3, 判断tableView是不是在可视的窗口范围内


```objc

@implementation LMJScrollWindow

static BOOL isRefresh_ = NO;

static UIWindow *window_ = nil;

+ (void)initialize
{
    window_ = [[UIWindow alloc] initWithFrame:LMJSharedApplication.statusBarFrame];
//    window_.backgroundColor = [UIColor redColor];
    window_.rootViewController = [[UIViewController alloc] initWithNibName:nil bundle:nil];
    window_.windowLevel = UIWindowLevelAlert;

    [window_ addGestureRecognizer:[[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(tapToTop)]];

    window_.hidden = NO;
}

+ (void)show
{
     window_.hidden = NO;
}

+ (void)tapToTop
{
    [self searchScrollViewInView:LMJKeyWindow];

}

+ (void)searchScrollViewInView:(UIView *)view
{

    for (UITableView *subView in view.subviews) {

//        CGRect newFrame = [subView.superview convertRect:subView.frame toView:LMJKeyWindow];
//
//        // 1没有隐藏, 2 frame交互 3, 控件没有透明 4 在主窗口上
//        BOOL isShowingOnWindow = !subView.isHidden && CGRectIntersectsRect(newFrame, LMJKeyWindow.bounds) && subView.alpha > 0.01 && subView.window == LMJKeyWindow;

        // 是tableview
        if([subView isKindOfClass:[UITableView class]] && subView.isShowingOnKeyWindow)
        {
            // 记录是否刷新
            if(isRefresh_ && subView.mj_header)
            {
                [subView.mj_header beginRefreshing];
                // 默认不能刷新
                isRefresh_ = NO;
            }else
            {
            [subView setContentOffset:CGPointMake(0, - subView.contentInset.top) animated:YES];
            }
        }
        else{

            [self searchScrollViewInView:subView];
        }
    }

}


// 刷新当前的tableview, 第一种方式重复点击的刷新
+ (void)refreshVisibleTableView
{
    // 希望找到的tableview能够刷新
    isRefresh_ = YES;
    [self searchScrollViewInView:LMJKeyWindow];
}

@end

```



### 在分类中判断有没有在主窗口显示
- 要在相同坐标系去判断

```objc
- (BOOL)isShowingOnKeyWindow
{
  // 1没有隐藏, 2 frame交互 3, 控件没有透明 4 在主窗口上

    CGRect newFrame = [LMJKeyWindow convertRect:self.frame fromView:self.superview];

//    CGRect new_frame = [self.superview convertRect:self.frame toView:LMJKeyWindow];

    BOOL isIntersects = CGRectIntersectsRect(LMJKeyWindow.bounds, newFrame);


    return !self.isHidden && self.alpha > 0.01 && self.window == LMJKeyWindow && isIntersects;

}

```



