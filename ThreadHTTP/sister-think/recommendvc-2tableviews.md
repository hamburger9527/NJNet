###recommendVc-2tableViews

### category-cell
- 可以设置里边控件的高亮属性
- 重写`-setSelected`, 控制子控件的高亮, 和状态
- 在`layoutsubView`里边布局cell自带的子控件, 可以设置cell-label自带的高度哦
- 不让cell选中`self.selectionStyle = UITableViewCellSelectionStyleNone`
- cell对应的xib里边设置一个左边的`指示器`, 在`-setSelected`里边控制隐藏与否
- 在cell的底部增加一个view的分割线
- label`可以设置高亮的文字颜色`

```objc

#import "LMJRecommendCategoryCell.h"
#import "LMJRecommendCategory.h"
@interface LMJRecommendCategoryCell ()
@property (weak, nonatomic) IBOutlet UIView *leftIndicatorView;

@end

@implementation LMJRecommendCategoryCell

static NSString *const categoryID = @"category";

+ (instancetype)categoryCellWithTableView:(UITableView *)tableView
{
    LMJRecommendCategoryCell *categoryCell = [tableView dequeueReusableCellWithIdentifier:categoryID];

    if(categoryCell == nil)
    {
        categoryCell = [[NSBundle mainBundle] loadNibNamed:NSStringFromClass(self) owner:nil options:nil].lastObject;
    }

    return categoryCell;
}
/**
 *  设置数据
 *
 *  @param category 模型数据
 */
- (void)setCategory:(LMJRecommendCategory *)category
{
    _category = category;

    self.textLabel.text = category.name;

}

/**
 *  一次性设置
 */
- (void)awakeFromNib {

    self.backgroundColor = LMJColorRBG(244, 244, 244, 1);

    /**
     *  高亮和不同状态下的文字的颜色
     */
    self.textLabel.highlightedTextColor = [UIColor colorWithRed:0.875 green:0.118 blue:0.059 alpha:1.000];
    self.textLabel.textColor = [UIColor colorWithWhite:0.361 alpha:1.000];

    /**
     *  不让cell选中 xib
     */
//    self.selectionStyle = UITableViewCellSelectionStyleNone;

}

/**
 *  重新布局cell里边的label
 */

- (void)layoutSubviews
{
    [super layoutSubviews];

    self.textLabel.y = 2;
    self.textLabel.height = self.contentView.height - 2 * self.textLabel.y;
}



- (void)setSelected:(BOOL)selected animated:(BOOL)animated {
    [super setSelected:selected animated:animated];

    self.textLabel.highlighted = selected;
    self.leftIndicatorView.hidden = !selected;

//    self.textLabel.textColor = selected ? self.leftIndicatorView.backgroundColor : [UIColor colorWithWhite:0.314 alpha:1.000];
}

@end


```


### category-Model
- 里边储存着每个分类ID属性对应的所有用户
- 里边储存着所有的可变数组的用户组
- 当前的页码和总数

```objc

@interface XMGRecommendCategory : NSObject
/** id */
@property (nonatomic, assign) NSInteger id;
/** 总数 */
@property (nonatomic, assign) NSInteger count;
/** 名字 */
@property (nonatomic, copy) NSString *name;

/** 这个类别对应的用户数据 */
@property (nonatomic, strong) NSMutableArray *users;
/** 总数 */
@property (nonatomic, assign) NSInteger total;
/** 当前页码 */
@property (nonatomic, assign) NSInteger currentPage;
@end

```

### userCell 和 userModel略过

## 在recommendVc里边的总结

- 0, 控制器的所有属性, 第一个宏非常重要

```objc

// 拿到左边选中的分类category模型,
#define LMJSelectedCategory self.categories[self.categoryTableView.indexPathForSelectedRow.row]
@interface LMJRecommendViewController ()<UITableViewDataSource, UITableViewDelegate>

/** 储存所有的分类模型, 里边储存的ID属性对应的用户组 */
@property (nonatomic, strong) NSArray<LMJRecommendCategory *> *categories;
@property (weak, nonatomic) IBOutlet UITableView *categoryTableView;
@property (weak, nonatomic) IBOutlet UITableView *userTableView;
/** <#digest#> */
@property (nonatomic, strong) AFHTTPSessionManager *mgr;

/** 为usertableVIew的刷新准备的 */
@property (nonatomic, strong) NSDictionary *params;

@end

```
- 1,因为有2个tableview为了防止顶部的内边距不好控制, 所以直接不让控制器设置了, 自己设置
    - 还可以设置tableView的滚动条的内间距
    - 取消tableView的分割线

```objc

- (void)setUpSettings
{
    self.navigationItem.title = @"我的推荐";

    self.automaticallyAdjustsScrollViewInsets = NO;

    self.categoryTableView.contentInset = UIEdgeInsetsMake(CGRectGetMaxY(self.navigationController.navigationBar.frame), 0, 0, 0);

    self.userTableView.contentInset = UIEdgeInsetsMake(CGRectGetMaxY(self.navigationController.navigationBar.frame), 0, 0, 0);

    self.view.backgroundColor = LMJControllerViewBgColor;

    // 解决滚动条被导航栏赶住了!!!
    self.userTableView.scrollIndicatorInsets = self.userTableView.contentInset;

    // 取消分割线
    self.userTableView.separatorStyle = UITableViewCellSeparatorStyleNone;

}

```

- 2,先给userTableView增加上啦和下拉的的头尾控件设置2个属性,

```objc
- (void)setUpHeaderFooterView
{
    self.userTableView.mj_header = [MJRefreshNormalHeader headerWithRefreshingTarget:self refreshingAction:@selector(headerPool)];

    self.userTableView.mj_header.automaticallyChangeAlpha = YES;


   self.userTableView.mj_footer = [MJRefreshAutoNormalFooter footerWithRefreshingTarget:self refreshingAction:@selector(footerPool)];
    self.userTableView.mj_footer.automaticallyHidden = YES;
}

```

- 3,拿到左边分类模型的所有数据, 仅仅是分类的模型, 里边没有用户, 只有ID属性,
        - 通过他发送请求能拿到所有的用户
    - 里边包括svprogressHud的使用
    - 请求成功后, 刷新左边的categoryTableView
    - 首先通过categoryTableView状态式选中第一行, 仅仅让这个第一行处于高亮状态
    - 然后手动调用category的代理方法, 选中第一行, 因为在这个方法中会发送请求,
            - 请求左边分类对应的右边的用户组


```objc

// 拿到左边的数据
- (void)getFirstData
{
    [SVProgressHUD showWithStatus:@"正在加载中!"];

    NSMutableDictionary *params = [NSMutableDictionary dictionary];

    params[@"a"] = @"category";
    params[@"c"] = @"subscribe";

    [self.mgr GET:LMJBaiSiJieHTTPAPI parameters:params progress:^(NSProgress * _Nonnull downloadProgress) {

        //        NSLog(@"%@", [NSThread currentThread]);

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {

        self.categories = [LMJRecommendCategory mj_objectArrayWithKeyValuesArray:responseObject[@"list"]];

        [self.categoryTableView reloadData];

        /**
         *  手动选中第一行分类, 处于高亮状态
         */
        [self.categoryTableView selectRowAtIndexPath:[NSIndexPath indexPathForRow:0 inSection:0] animated:YES scrollPosition:UITableViewScrollPositionNone];

        // 手动调用代理方法, 加载第一页数据
        [self tableView:self.categoryTableView didSelectRowAtIndexPath:[NSIndexPath indexPathForRow:0 inSection:0]];

        [SVProgressHUD dismiss];

    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {

        [SVProgressHUD showErrorWithStatus:@"加载数据失败"];

//        LMJLog(@"%@", error);

        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{

            [SVProgressHUD dismiss];

        });
    }];

}

```

- 4 在categoryTableView的选中方法中刷新数据
    - 每次选择新的分类, 就把之前的头尾视图的状态清空
    - 立即刷新usertableView, 就算是空白也要这样做,
        - 免得用户点击后因为网络延迟还是看到的是当个分类的数据
    - 因为在这个方法中是属于下拉, 刷新, 是请求新的数据, 为了防止用户重复点击同一个分类的重复刷新
        - 所以需要在这个方法中判断用户是否已经选中了当前所在的分类
        - 如果大于0, 就不再去请求下拉<-headerpool>网络数据

```objc

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    // 每次选择新的分类, 就把之前的头尾视图的状态清空
    [self stateOfHeaderFooterView];
    // 赶紧刷新表格,目的是: 马上显示当前category的用户数据, 不让用户看见上一个category的残留数据
    // 立即跳转到下一个分类, 解决网络延迟
    [self.userTableView reloadData];

    LMJRecommendCategory *category = self.categories[indexPath.row];

    // 如果大于0, 就不再去请求下拉<-headerpool>网络数据,
    if(category.users.count > 0) return;

    [self.userTableView.mj_header beginRefreshing];

}

```

- 5 控制usertableVIew的头尾视图的状态
    - 如果当前选中的分类请求的所获的用户数量大于category模型中服务器返回的用户总数属性,
        - 就设置`MJRefreshStateNoMoreData`
        - 否则就让footer处于普同状态

```objc

// 设置footerView的状态
- (void)stateOfHeaderFooterView
{
//    LMJRecommendCategory *category = LMJSelectedCategory;

    if(LMJSelectedCategory.users.count >= LMJSelectedCategory.total)
    {
        [self.userTableView.mj_footer setState:MJRefreshStateNoMoreData];
    }else
    {
        [self.userTableView.mj_footer setState:MJRefreshStateIdle];
    }


    if([self.userTableView.mj_header isRefreshing])
    {
        [self.userTableView.mj_header endRefreshing];
    }

    if(self.userTableView.mj_footer.isRefreshing)
    {
        [self.userTableView.mj_footer endRefreshing];
    }
}

```

- 6, 下拉请求新的数据
    - 首先停止gooter的刷新状态
    - 根据左侧选中的的分类category中的ID属性去服务器请求用户数据
    - 记录操作, 防止用户的反复请求导致刷新tableview'的时候时候出现问题
    - 如果加载成功, 就设置选中的category模型中的页码为1
    - 清空以前的数据, 储存新的数据
    - category模型记录服务器返回的对应的分类用户组总数
    - 刷新usertableVIew,
    - 结束头部视图的刷新状态,
        - 判断用户的总数是否达到服务器返回的总数属性,设置footer的状态

```objc
- (void)headerPool
{
    if(self.userTableView.mj_footer.isRefreshing) [self.userTableView.mj_footer endRefreshing];

//    LMJRecommendCategory *category = LMJSelectedCategory;

    // 否则就去网络中加载
    NSMutableDictionary *params = [NSMutableDictionary dictionary];

    params[@"a"] = @"list";
    params[@"c"] = @"subscribe";
    params[@"category_id"] = @(LMJSelectedCategory.ID);


    self.params = params;

    [self.mgr GET:LMJBaiSiJieHTTPAPI parameters:params progress:^(NSProgress * _Nonnull downloadProgress) {
        if(self.params != params) return ;

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        if(self.params != params) return ;

        //分类加载第一页数据成功
        LMJSelectedCategory.page = 1;

        //移除以前的数据
        [LMJSelectedCategory.users removeAllObjects];

        [LMJSelectedCategory.users addObjectsFromArray:[LMJRecommendUser mj_objectArrayWithKeyValuesArray:responseObject[@"list"]]];
        /**
         *  记录总数
         */
        LMJSelectedCategory.total = [responseObject[@"total"] integerValue];


        [self.userTableView reloadData];

        [self stateOfHeaderFooterView];

    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        if(self.params != params) return ;
        [self stateOfHeaderFooterView];
        [SVProgressHUD showErrorWithStatus:@"加载数据失败"];
    }];
}

```

- 7, 下拉刷新
    - 结束头部视图的刷新状态
    - 页码 `NSInteger page = LMJSelectedCategory.page + 1`, 请求下一页的数据
    - 记录操作, 防止用户反复请求造成tableview的刷新的重复
    - 为category模型增加用户数据
    - 刷新usertableVIew
    - 判断尾部视图的状态, 看用户总数有木有达到

```objc
// 拉拽尾部视图
- (void)footerPool
{
    if(self.userTableView.mj_header.isRefreshing) [self.userTableView.mj_header endRefreshing];

//    LMJRecommendCategory *category = LMJSelectedCategory;

    // 否则就去网络中加载
    NSMutableDictionary *params = [NSMutableDictionary dictionary];

    //并且让分类加载下一页数据
    NSInteger page = LMJSelectedCategory.page + 1;

    params[@"a"] = @"list";
    params[@"c"] = @"subscribe";
    params[@"category_id"] = @(LMJSelectedCategory.ID);
    // 加载下一页
    params[@"page"] = @(page);

    self.params = params;

    [self.mgr GET:LMJBaiSiJieHTTPAPI parameters:params progress:^(NSProgress * _Nonnull downloadProgress) {
         if(self.params != params) return ;

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        if(self.params != params) return ;

        [LMJSelectedCategory.users addObjectsFromArray:[LMJRecommendUser mj_objectArrayWithKeyValuesArray:responseObject[@"list"]]];

        LMJSelectedCategory.page = page;

        //        NSLog(@"%@", responseObject);
        [self.userTableView reloadData];

        [self stateOfHeaderFooterView];

    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
         if(self.params != params) return ;

        [self stateOfHeaderFooterView];
        [SVProgressHUD showErrorWithStatus:@"加载数据失败"];
    }];
}

```

- 8, 在这个方法中返回行数, 根据右边选中的category模型, 从中拿到用户数据返回usertableVIew的行数

```objc

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    if(tableView == self.categoryTableView)
    {
        return self.categories.count;
    }
    else
    {
        return LMJSelectedCategory.users.count;
    }
}
```

- 9 ,细节问题, 用户进入, 突然返回上个控制器, 的时候HUD的存在问题
    - 控制器死的时候取消所有的请求

```objc
// 当控制器销毁的时候, 取消所有的请求
- (void)dealloc
{
    [self.mgr.operationQueue cancelAllOperations];
}

/**
 *  解决用户突然返回, 仍然存在HUD
 */
- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];

    [SVProgressHUD dismiss];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    if(self.categoryTableView == tableView)
    {
        LMJRecommendCategory *category = self.categories[indexPath.row];

        LMJRecommendCategoryCell *categoryCell = [LMJRecommendCategoryCell categoryCellWithTableView:tableView];

        categoryCell.category = category;

        return categoryCell;
    }else
    {
        LMJRecommendUser *user = LMJSelectedCategory.users[indexPath.row];

        LMJRecommendUserCell *userCell = [LMJRecommendUserCell userCellWithTableView:tableView];

        userCell.user = user;

        return userCell;
    }

}

```

