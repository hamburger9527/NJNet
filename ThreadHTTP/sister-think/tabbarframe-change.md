# tabBarFrame-Change

### 利用kvc更改tabBarViewController的tabbar

```objc

    // 把系统的tababr设置为自定义的tababr
    //    self.tabBar = [[LMJTabBar alloc] init];
    // 利用KVC
    [self setValue:[[LMJTabBar alloc] init] forKey:@"tabBar"];

```

### 设置 `LMJTabBar`, 在里边发通知外界我点击了哪个按钮的索引, 并且布局里边的子控件

```objc
#import "LMJTabBar.h"
#import "LMJGuideTool.h"

@interface LMJTabBar ()

/** 中间的发布按钮 */
@property (weak, nonatomic) UIButton *publishButton;

@end

@implementation LMJTabBar
/**
 *  设置中间的按钮, 并且布局子控件
 */

- (instancetype)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {

        [self setBackgroundImage:[UIImage imageStrechedWithImageName:@"tabbar-light"]];
        /**
         *  添加中间的按钮
         */
        UIButton *publishButton = [UIButton buttonWithType:UIButtonTypeCustom];
//        publishButton.backgroundColor = [UIColor redColor];
        [self addSubview:publishButton];
        self.publishButton = publishButton;

        [publishButton setBackgroundImage:[UIImage imageNamed:@"tabBar_publish_icon"] forState:UIControlStateNormal];

        [publishButton setBackgroundImage:[UIImage imageNamed:@"tabBar_publish_click_icon"] forState:UIControlStateHighlighted];

        [publishButton addTarget:self action:@selector(publishShow) forControlEvents:UIControlEventTouchUpInside];

    }
    return self;
}



- (void)layoutSubviews
{
    [super layoutSubviews];

    CGFloat buttonX = 0;
    CGFloat buttonY = 0;

    CGFloat buttonW = self.width / (self.items.count + 1);
    CGFloat buttonH = self.height;

    NSInteger index = 0;

    /**
     *  设置UITabBarButton
     */
    for (UIView *button in self.subviews)
    {
        if([button isKindOfClass:NSClassFromString(@"UITabBarButton")])
        {
            buttonX = buttonW * (index > 1 ? (index + 1) : index);
            button.frame = CGRectMake(buttonX, buttonY, buttonW, buttonH);
            index++;
        }
    }



    /**
     *  设置中间按钮的尺寸和位置
     */

//    self.publishButton.bounds = CGRectMake(0, 0, self.publishButton.currentBackgroundImage.size.width, self.publishButton.currentBackgroundImage.size.height);

//    self.publishButton.size = self.publishButton.currentBackgroundImage.size;

    [self.publishButton sizeToFit];
    // 注意设置center
    self.publishButton.center = CGPointMake(self.width / 2, self.height/2);

}

// 转到发布界面
- (void)publishShow
{
    [LMJGuideTool guideToPublish];
}

/**
 *  发通知给lmjtopicVc, 点击了tabar
 *
 *  @param selectedItem 选中了哪个item
 */
- (void)setSelectedItem:(UITabBarItem *)selectedItem
{
    [super setSelectedItem:selectedItem];


    NSDictionary *info = @{
                           LMJTabBarControllerDidSelectedIndex : @([self.items indexOfObject:selectedItem])
                           };

    [LMJNotiDefaultCenter postNotificationName:LMJTabBarControllerDidSelectedNotification object:nil userInfo:info];

}


@end

```


### 为view增加分类

```objc

@interface UIView (XMGExtension)
@property (nonatomic, assign) CGFloat width;
@property (nonatomic, assign) CGFloat height;
@property (nonatomic, assign) CGFloat x;
@property (nonatomic, assign) CGFloat y;

//- (CGFloat)x;
//- (void)setX:(CGFloat)x;
/** 在分类中声明@property, 只会生成方法的声明, 不会生成方法的实现和带有_下划线的成员变量*/
@end

```
