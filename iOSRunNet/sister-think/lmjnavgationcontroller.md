# UIBarButtonItem的设置, 争加分类

### 它的跟控制器设置按钮的时候, 和一边设置2个按钮的时候, 是从右往左排布的

```objc

    // 设置导航栏标题
    self.navigationItem.title = @"我的";

    // 设置导航栏右边的按钮
    UIButton *settingButton = [UIButton buttonWithType:UIButtonTypeCustom];
    [settingButton setBackgroundImage:[UIImage imageNamed:@"mine-setting-icon"] forState:UIControlStateNormal];
    [settingButton setBackgroundImage:[UIImage imageNamed:@"mine-setting-icon-click"] forState:UIControlStateHighlighted];

    // 拿到当前的按钮的图片直接给按钮设置尺寸
    settingButton.size = settingButton.currentBackgroundImage.size;

    [settingButton addTarget:self action:@selector(settingClick) forControlEvents:UIControlEventTouchUpInside];

    UIButton *nightModeButton = [UIButton buttonWithType:UIButtonTypeCustom];
    [nightModeButton setBackgroundImage:[UIImage imageNamed:@"mine-moon-icon"] forState:UIControlStateNormal];
    [nightModeButton setBackgroundImage:[UIImage imageNamed:@"mine-moon-icon-click"] forState:UIControlStateHighlighted];

    // 拿到当前的按钮的图片直接给按钮设置尺寸
    nightModeButton.size = nightModeButton.currentBackgroundImage.size;
    [nightModeButton sizeToFit]

    [nightModeButton addTarget:self action:@selector(nightModeClick) forControlEvents:UIControlEventTouchUpInside];

    // 右边的排布是从右往左排布
    self.navigationItem.rightBarButtonItems = @[
                                    [[UIBarButtonItem alloc] initWithCustomView:settingButton],
                                    [[UIBarButtonItem alloc] initWithCustomView:nightModeButton]
                                                ];

```

### `UIBarButtonItem` 增加分类方法

```objc
+ (instancetype)itemWithImage:(NSString *)image highLightedImage:(NSString *)highLightedImage target:(id)target selector:(SEL)selector
{
    UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
    [button addTarget:target action:selector forControlEvents:UIControlEventTouchUpInside];

    [button setBackgroundImage:[UIImage imageNamed:image] forState:UIControlStateNormal];

    [button setBackgroundImage:[UIImage imageNamed:highLightedImage] forState:UIControlStateHighlighted];

    // 可以拿到当前的图片设置尺寸
//    button.size = button.currentBackgroundImage.size;
    [button sizeToFit];

    return [[UIBarButtonItem alloc] initWithCustomView:button];
}

```

