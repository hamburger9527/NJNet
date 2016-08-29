# LMJNavgationVC-Push-backBarButtonItem

### 设置导航控制器的`跟控制器`显示tabar, push到其他控制器不显示,
### 并且设置返回按钮
### 设置UINavigationBar的左右2边按钮的富文本, 中间标题的富文本, 和navigationBar的背景

```objc
+ (void)initialize
{
    /**
     *  设置导航条的背景
     */
    UINavigationBar *navigationBar = [UINavigationBar appearanceWhenContainedIn:self, nil];
    [navigationBar setBackgroundImage:[UIImage imageStrechedWithImageName:@"navigationbarBackgroundWhite"] forBarMetrics:UIBarMetricsDefault];
    navigationBar.backgroundColor = [UIColor clearColor];


    // 决定了标题的富文本属性, 中间的
    [navigationBar setTitleTextAttributes:@{NSForegroundColorAttributeName : [UIColor redColor], NSFontAttributeName : [UIFont boldSystemFontOfSize:20]}];

    // 决定了两边的标题未选中的颜色
    //    navigationBar.tintColor = [UIColor blackColor];

    // 用这个的话可以设置富文本属性, 两边的按钮
    UIBarButtonItem *item = [UIBarButtonItem appearanceWhenContainedIn:self, nil];

    [item setTitleTextAttributes:@{NSForegroundColorAttributeName : [UIColor blackColor], NSFontAttributeName : [UIFont systemFontOfSize:17]} forState:UIControlStateNormal];

    [item setTitleTextAttributes:@{NSForegroundColorAttributeName : [UIColor lightGrayColor]} forState:UIControlStateDisabled];

}

/**
 * 可以在这个方法中拦截所有push进来的控制器
 */
- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated
{
    /**
     *  非根控制器添加一个返回按钮, 并且隐藏底部的tabBar
     */
    if(self.childViewControllers.count > 0)
    {
        /**
         *  隐藏底部的bar
         */
        viewController.hidesBottomBarWhenPushed = YES;

        /**
         *  设置左边的返回按钮
         */
        viewController.navigationItem.leftBarButtonItem = [UIBarButtonItem itemBackBarButtonWithTarget:self selector:@selector(popBack)];

        /**
         *  边缘侧滑的时候需要返回
         */
        self.interactivePopGestureRecognizer.delegate = nil;
    }

    /**
     *  在这里push的话, 之前没有用到控制器的view, 就不会调用viewDidLoad
     * 在个方法pushViewController后才会调用, 在控制器内部自定义的配置可以起
     * 效
     // 这句super的push要放在后面, 让viewController里边的自定义可以覆盖上面设置的leftBarButtonItem

     */
    [super pushViewController:viewController animated:animated];
}

```





### 设置全局侧滑返回, 运行时取出属性, 直接调用`getSystemGestureOfBack`

```objc
/**
 *  设置系统的全局返回
 */
- (void)getSystemGestureOfBack
{
    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self.interactivePopGestureRecognizer.delegate action:NSSelectorFromString(@"handleNavigationTransition:")];

//    NSLog(@"%@", self.interactivePopGestureRecognizer);

    [self.view addGestureRecognizer:pan];

    // 跟控制器的时候不能滑动
    pan.delegate = self;
}
// 跟控制器的时候不能滑动
- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer
{
    return (!(self.childViewControllers.firstObject == self.topViewController));
}

```

###设置返回按钮的一些东东, 里边按钮的属性`contentHorizontalAlignment`和`contentEdgeInsets`
```objc
/**
 *  设置左边的返回按钮
 */
+ (UIBarButtonItem *)itemBackBarButtonWithTarget:(id)target selector:(SEL)selector
{
    // 设置按钮
    UIButton *backButton = [UIButton buttonWithType:UIButtonTypeCustom];
    [backButton setImage:[UIImage imageNamed:@"navigationButtonReturn"] forState:UIControlStateNormal];
    [backButton setImage:[UIImage imageNamed:@"navigationButtonReturnClick"] forState:UIControlStateHighlighted];
    [backButton setTitle:@"返回" forState:UIControlStateNormal];

    [backButton setTitleColor:[UIColor blackColor] forState:UIControlStateNormal];
    [backButton setTitleColor:[UIColor redColor] forState:UIControlStateHighlighted];
    //        backButton.backgroundColor = [UIColor grayColor];
    [backButton sizeToFit];
    // 内容水平左对齐
    backButton.contentHorizontalAlignment = UIControlContentHorizontalAlignmentLeft;

    // 按钮里边的内容继续向左10靠并且超出
    backButton.contentEdgeInsets = UIEdgeInsetsMake(0, -10, 0, 0);
    //        LMJLog(@"%@", NSStringFromCGRect(backButton.frame));
//    [backButton setImageEdgeInsets:<#(UIEdgeInsets)#>];

    [backButton addTarget:target action:selector forControlEvents:UIControlEventTouchUpInside];

    return [[UIBarButtonItem alloc] initWithCustomView:backButton];
}

```


###设置边缘侧滑返

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160731_16.png)

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160731_17.png)

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160731_18.png)


## 所有代码

```objc
#import "LMJNavigationController.h"

@interface LMJNavigationController ()<UIGestureRecognizerDelegate, UINavigationControllerDelegate>

/** popdelegage */
@property (nonatomic, strong) id systemPopDelegate;

@end


@implementation LMJNavigationController

- (void)viewDidLoad
{
    [super viewDidLoad];

    /**
     *  设置系统的全局返回
     */
//    [self getSystemGestureOfBack];

    /**
     *  边缘侧滑
     */
    self.systemPopDelegate = self.interactivePopGestureRecognizer.delegate;
    self.delegate = self;
}

/**
 *  设置系统的全局返回================================================================
 */
- (void)getSystemGestureOfBack
{
    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self.interactivePopGestureRecognizer.delegate action:NSSelectorFromString(@"handleNavigationTransition:")];

//    NSLog(@"%@", self.interactivePopGestureRecognizer);

    [self.view addGestureRecognizer:pan];

    // 跟控制器的时候不能滑动
    pan.delegate = self;
}
// 跟控制器的时候不能滑动
- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer
{
    return (!(self.childViewControllers.firstObject == self.topViewController));
}
//==================================================================================



+ (void)initialize
{
    /**
     *  设置导航条的背景
     */
    UINavigationBar *navigationBar = [UINavigationBar appearanceWhenContainedIn:self, nil];
    [navigationBar setBackgroundImage:[UIImage imageStrechedWithImageName:@"navigationbarBackgroundWhite"] forBarMetrics:UIBarMetricsDefault];
    navigationBar.backgroundColor = [UIColor clearColor];


    // 决定了标题, 中间的
    [navigationBar setTitleTextAttributes:@{NSForegroundColorAttributeName : [UIColor redColor], NSFontAttributeName : [UIFont boldSystemFontOfSize:20]}];

    // 决定了两边的标题未选中的颜色
    //    navigationBar.tintColor = [UIColor blackColor];

    // 用这个的话可以设置富文本属性, 两边的按钮
    UIBarButtonItem *item = [UIBarButtonItem appearanceWhenContainedIn:self, nil];

    [item setTitleTextAttributes:@{NSForegroundColorAttributeName : [UIColor blackColor], NSFontAttributeName : [UIFont systemFontOfSize:17]} forState:UIControlStateNormal];

    [item setTitleTextAttributes:@{NSForegroundColorAttributeName : [UIColor lightGrayColor]} forState:UIControlStateDisabled];

}

/**
 * 可以在这个方法中拦截所有push进来的控制器
 */
- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated
{
    /**
     *  非根控制器添加一个返回按钮, 并且隐藏底部的tabBar
     */
    if(self.childViewControllers.count > 0)
    {
        /**
         *  隐藏底部的bar
         */
        viewController.hidesBottomBarWhenPushed = YES;

        /**
         *  设置左边的返回按钮
         */
        viewController.navigationItem.leftBarButtonItem = [UIBarButtonItem itemBackBarButtonWithTarget:self selector:@selector(popBack)];

        /**
         *  边缘侧滑的时候需要返回
         */
        self.interactivePopGestureRecognizer.delegate = nil;
    }

    /**
     *  在这里push的话, 之前没有用到控制器的view, 就不会调用viewDidLoad
     * 在个方法pushViewController后才会调用, 在控制器内部自定义的配置可以起
     * 效
     // 这句super的push要放在后面, 让viewController可以覆盖上面设置的leftBarButtonItem

     */
    [super pushViewController:viewController animated:animated];
}


- (void)popBack
{
    [self popViewControllerAnimated:YES];
}

/**
 *  在pop到跟控制器的时候还原代理======边缘侧滑======================================
 */

- (void)navigationController:(UINavigationController *)navigationController didShowViewController:(UIViewController *)viewController animated:(BOOL)animated
{
    if(viewController == self.childViewControllers.firstObject)
    {
        self.interactivePopGestureRecognizer.delegate = self.systemPopDelegate;
    }
}
//=============================================================

```

