# LMJTopicViewController

- 设置一些自己的tableview的属性
    - 当自己的tableview添加到其他控制器的view上边的时候, frame.y为20, 注意点

```objc
// 设置控制器tableview的一些属性
- (void)setupTableViewSettings
{
// 清空系统没人的frame
    self.view.frame = LMJMainScreen.bounds;

//设置顶部的内间距为导航控制的导航条的最大高度和titleview的高度
    self.tableView.contentInset = UIEdgeInsetsMake(CGRectGetMaxY(self.parentViewController.navigationController.navigationBar.frame) + LMJTitlesViewInEssenceHeight, 0, self.parentViewController.tabBarController.tabBar.height, 0);

    self.tableView.backgroundColor = [UIColor clearColor];
// 滚动条的内间距
    self.tableView.scrollIndicatorInsets = self.tableView.contentInset;
// 设置分割线的样式
    self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;

    //注册当tabarVC选中的时候时候发出的通知, 不需要接受数据
    [LMJNotiDefaultCenter addObserver:self selector:@selector(tabBarClick:) name:LMJTabBarControllerDidSelectedNotification object:nil];

}

```
