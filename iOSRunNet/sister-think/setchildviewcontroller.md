# setChildViewController

### 设置控制器的一些共有属性, 在这里不要访问控制器的view, 它是懒加载的

```objc
/**
 *  自定义方法添加自控制器
 */
- (void)addChildViewController:(UIViewController *)childController title:(NSString *)title image:(NSString *)image selectedImage:(NSString *)selectedImage
{
    childController.navigationItem.title = title;
    childController.tabBarItem.title = title;
    childController.tabBarItem.image = [UIImage imageOriginallyWithImageName:image];
    childController.tabBarItem.selectedImage = [UIImage imageNamed:selectedImage];

    // 在这里会加载loadview, 并且设置里边的标题
    //    childController.view.backgroundColor = [self randomColor];

    // 如果在上一步之后再执行 childController.navigationItem.title = title;
    // 那么自定义的标题会被覆盖
    // 注意

    /**
     *  添加自控制器, 并且包装成导航控制器
     */
    [self addChildViewController:[[LMJNavigationController alloc] initWithRootViewController:childController]];
}

```
