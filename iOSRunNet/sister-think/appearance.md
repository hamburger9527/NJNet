# appearance, 设置tabar条上边的属性

```objc
/**
 *  第一次使用这个类的时候调用
 */
+ (void)initialize
{
    /**
     *  一次性设置tababr上边按钮的字体颜色和选中的字体颜色
     */
    // 通过appearance统一设置所有UITabBarItem的文字属性
    // 后面带有UI_APPEARANCE_SELECTOR的方法, 都可以通过appearance对象来统一设置
    UITabBarItem *item = [UITabBarItem appearanceWhenContainedIn:self, nil];

    NSMutableDictionary *dictM = [NSMutableDictionary dictionary];
    dictM[NSFontAttributeName] = [UIFont systemFontOfSize:12];
    dictM[NSForegroundColorAttributeName] = [UIColor colorWithRed:0.541 green:0.529 blue:0.529 alpha:1.000];

    [item setTitleTextAttributes:dictM forState:UIControlStateNormal];


    NSMutableDictionary *selectedDictM = [NSMutableDictionary dictionary];
    selectedDictM[NSFontAttributeName] = dictM[NSFontAttributeName];
    selectedDictM[NSForegroundColorAttributeName] = [UIColor colorWithWhite:0.251 alpha:1.000];

    [item setTitleTextAttributes:selectedDictM forState:UIControlStateSelected];

    // 选中的颜色, 不能决定状态和字体
    //    tabBar.tintColor = [UIColor redColor];

//    /**
//     *  设置底部条的背景图片
//     */
//    UITabBar *tabBar = [UITabBar appearanceWhenContainedIn:self, nil];
//    tabBar.backgroundColor = [UIColor clearColor];
//
//    [tabBar setBackgroundImage:[UIImage imageStrechedWithImageName:@"tabbar-light"]];


}

```
