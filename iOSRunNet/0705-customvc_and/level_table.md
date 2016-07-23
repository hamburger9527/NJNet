# 级联菜单

## 第一种方法, `用2个tableviewController`的view添加到一另外控制器的View上边

- 1, 怎么设置cell的高亮图片和高亮文字的颜色

```objc
// 当一个cell被选中的时候，cell内部的子控件都会达到highlighted状态
    // 设置普通图片
    cell.imageView.image = [UIImage imageNamed:c.icon];
    // 设置高亮图片（cell选中 -> cell.imageView.highlighted = YES -> cell.imageView显示highlightedImage这个图片）
    cell.imageView.highlightedImage = [UIImage imageNamed:c.highlighted_icon];

    // 设置label高亮时的文字颜色
    cell.textLabel.highlightedTextColor = [UIColor redColor];

```

- 2, 一定要把2个控制器添加成另一个控制器的子控制器;强引用控制器别死了

```objc

- (void)viewDidLoad {
    [super viewDidLoad];

    CGFloat width = self.view.frame.size.width * 0.5;
    CGFloat height = self.view.frame.size.height;

    XMGSubcategoryViewController *subcategoryVc = [[XMGSubcategoryViewController alloc] init];
    subcategoryVc.view.frame = CGRectMake(width, 0, width, height);
    [self addChildViewController:subcategoryVc];
    [self.view addSubview:subcategoryVc.view];

    XMGCategoryViewController *categoryVc = [[XMGCategoryViewController alloc] init];
    categoryVc.delegate = subcategoryVc;
    categoryVc.view.frame = CGRectMake(0, 0, width, height);
    [self addChildViewController:categoryVc];
    [self.view addSubview:categoryVc.view];
}

```

- 3, 通过代理传递数据,选中的时候通知代理做事情, 并且传递数据` categoryVc.delegate = subcategoryVc;`

```objc
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    // 告诉代理
    if ([self.delegate respondsToSelector:@selector(categoryViewController:didSelectSubcategories:)]) {
        XMGCategory *c = self.categories[indexPath.row];
        [self.delegate categoryViewController:self didSelectSubcategories:c.subcategories];
    }
}

```
 - 4,代理做事情

```objc
#pragma mark - <XMGCategoryViewControllerDelegate>
- (void)categoryViewController:(XMGCategoryViewController *)categoryViewController didSelectSubcategories:(NSArray *)subcategories
{
    self.subcategories = subcategories;

    [self.tableView reloadData];
}

```

## 第二种方法, `用2个tableview` 添加到一另外控制器的View上边, 设置控制器成为他们2个的数据源, 控制器成为`主菜单tableview`的`代理`


- 在sb中注册2个tableView的动态cell
- 在.m中直接使用

- 副菜单的数据根据第一个主菜单的选中索引加载数据
- 当选中主菜单的cell的时候, 通知副菜单刷新

```objc

#pragma mark - Table view data source
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    // 左边表格
    if (tableView == self.categoryTableView) return self.categories.count;

    // 右边表格
    XMGCategory *c = self.categories[self.categoryTableView.indexPathForSelectedRow.row];
    return c.subcategories.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    // 左边表格
    if (tableView == self.categoryTableView) {
        static NSString *ID = @"category";
        UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];


        XMGCategory *c = self.categories[indexPath.row];

        // 设置普通图片
        cell.imageView.image = [UIImage imageNamed:c.icon];
        // 设置高亮图片（cell选中 -> cell.imageView.highlighted = YES -> cell.imageView显示highlightedImage这个图片）
        cell.imageView.highlightedImage = [UIImage imageNamed:c.highlighted_icon];

        // 设置label高亮时的文字颜色
        cell.textLabel.highlightedTextColor = [UIColor redColor];

        cell.textLabel.text = c.name;
        cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;


        return cell;
    } else {
        // 右边表格
        static NSString *ID = @"subcategory";
        UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];

        // 获得左边表格被选中的模型
        XMGCategory *c = self.categories[self.categoryTableView.indexPathForSelectedRow.row];
        cell.textLabel.text = c.subcategories[indexPath.row];

        return cell;
    }
}

#pragma mark - <UITableViewDelegate>
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    if (tableView == self.categoryTableView) {
        [self.subcategoryTableView reloadData];
    }
}


```


















