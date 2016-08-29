# 0705自定义控制器的切换\级联菜单


## 控制器父子关系的建立原则
- 1 如果2个控制器的view是父子关系(不管是直接还是间接的父子关系)，那么这2个控制器也应该为父子关系

```objc
[a.view addSubview:b.view];
[a addChildViewController:b];
// 或者
[a.view addSubview:otherView];
[otherView addSubbiew.b.view];
[a addChildViewController:b];
```
- 2 成为父子控制器后, 系统的重要通知比如`屏幕旋转`, 才能通过`父控制器`传递给`子控制器`

```objc
/**
 * 屏幕即将旋转到某个方向时会调用这个方法
 */
- (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
{
    NSLog(@"%@ willRotateToInterfaceOrientation", self.class);
}

```
- 3 成为父子控制器后, 当子控制器的view在父控制器的view上边的时候, 子控制器才能拿到父控制器的导航控制器来进行`push`

```objc
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    XMGTempViewController *temp = [[XMGTempViewController alloc] init];
    // 拿到的是父控制器的导航控制器
    [self.navigationController pushViewController:temp animated:YES];
}

```
- 4 成为父子控制器后, 当子控制器的view在父控制器的view上边的时候, 父控制器是`present`出来的时候,
    点击子控制器的view, `能代理帮助父控制器dismiss`

```objc
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    [self dismissViewControllerAnimated:YES completion:nil];
}

```


## 获得所有的子控制器
```objc
@property(nonatomic,readonly) NSArray *childViewControllers;
```

## 添加一个子控制器
- 手动添加, 不会自动调用`- (void)didMoveToParentViewController:`, 需要手动调用
- 如果一个控制器`embed in`了一个导航控制器, 在系统启动的时候, 会自动调用子控制器`- (void)didMoveToParentViewController:`

```objc
//XMGOneViewController成为了self的子控制器
//self成为了XMGOneViewController的父控制器
[self addChildViewController:[[XMGOneViewController alloc] init]];
// 通过addChildViewController添加的控制器都会存在于childViewControllers数组中
```

## 获得父控制器
```objc
@property(nonatomic,readonly) UIViewController *parentViewController;
```

## 将一个控制器从它的父控制器中移除
- 当把控制器手动移除后, 系统会自动调用`- (void)didMoveToParentViewController:`, 传入的参数是`nil`

```objc
// 控制器a从它的父控制器中移除
[a removeFromParentViewController];
```

- 当前控制器已经被添加到某个父控制器上时就会调用这个方法

```objc
- (void)didMoveToParentViewController:(UIViewController *)parent
{
    [super didMoveToParentViewController:parent];

    NSLog(@"didMoveToParentViewController - %@", parent);
}

```

## 所有控制器view的autoresizingMask属性
- 注意, 如果从xib或者sb中加载的view算frame的时候, 大小会出现问题
 - 都是从xib加载的时候哦!!!!!!纯代码添加到sb中不会出现问题.
    - 如果不想子控件的尺寸随便变可以设置` one.view.autoresizingMask = UIViewAutoresizingNone`
    - self.view和one.view都是从xib加载的, 从xib加载的时候, 尺寸还是xib得尺寸, 只是xib内部的子控件会随着xib-View的拉伸而拉伸
![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160627_5.png)

