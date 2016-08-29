# MJRefresh-full, 最完善的用法

- 1, 上拉或者下拉的时候, 结束头部或者尾部刷新,防止用户同时上拉和下拉刷新endRefreshing
- 2, 记录请求params, 防止上拉和下拉同时请求后刷新tableview
- 3, 记录页码数, 如果下拉, 就是刷新数据, 并且把之前的数据清空,就在请求成功后再把页码清0
- 4, 如果上拉, @(页码+1), 就是加载下一页的数据, 就在请求成功后再把(页码 += 1),
    - 同样要把以前的数组清空
- 5, 一进来的时候就`self.collectionView.mj_header beginRefreshing]`
- 6, 请求成功储存数据后再[tableView reloadData]
- 7, 记录请求成功后一maxtime, 以便请求下一页
- 8, 结束刷新状态
- 9, 如果请求失败, 就提示用户加载失败, 同样结束刷新
- 10,判断有没有更多的数据, 设置footer的状态
- 11,在控制器死的时候要取消所有的请求

## 需要的属性

```objc
NSString *const LMJBaiSiJieHTTPAPI = @"http://api.budejie.com/api/api_open.php";
NSString *const LMJTopicType = @"10";

@interface LMJShopViewController () <LMJWaterflowLayoutDelegate>

/** 所有的模型数据 */
@property (nonatomic, strong) NSMutableArray *shops;

/** manager */
@property (nonatomic, strong) AFHTTPSessionManager *manager;

/** 以最后一次的请求为准 */
@property (nonatomic, strong) NSDictionary *params;

/** 第几页的数据 */
@property (assign, nonatomic) NSInteger page;

/** 记录上次返回的maxtime, 加载下一页 */
@property (nonatomic, copy) NSString *maxtime;

@end

```

## 实现

```objc

- (void)addHeaderFooterView
{
    self.collectionView.mj_header = [MJRefreshNormalHeader headerWithRefreshingTarget:self refreshingAction:@selector(headerPull)];
    self.collectionView.mj_header.automaticallyChangeAlpha = YES;

    [self.collectionView.mj_header beginRefreshing];



    self.collectionView.mj_footer = [MJRefreshAutoNormalFooter footerWithRefreshingTarget:self refreshingAction:@selector(footerPull)];
    self.collectionView.mj_footer.automaticallyHidden = YES;

}

- (void)headerPull
{
    if(self.collectionView.mj_footer.isRefreshing) [self.collectionView.mj_footer endRefreshing];

    NSMutableDictionary *params = [NSMutableDictionary dictionary];

    params[@"a"] = @"list";
    params[@"c"] = @"data";
    params[@"type"] = LMJTopicType;

    // 记录操作
    self.params = params;
    // 第一次加载帖子时候不需要传入此关键字，当需要加载下一页时：需要传入加载上一页时返回值字段“maxtime”中的内容
//    params[@"maxtime"] = @"";
//    当前所加载的页码数，默认为0
//    params[@"page"] = @"0";


    [self.manager GET:LMJBaiSiJieHTTPAPI parameters:params progress:^(NSProgress * _Nonnull downloadProgress) {
        if (self.params != params) return ;


    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        if (self.params != params) return ;

        // 清空数据, 并且添加最新的数据
        self.shops = [LMJShop mj_objectArrayWithKeyValuesArray:responseObject[@"list"]];
        // 记录返回的maxtime
        self.maxtime = responseObject[@"info"][@"maxtime"];

        // 页码归零
        self.page = 0;

        // 刷新collection
        [self.collectionView reloadData];

        // 结束header的刷新
        [self.collectionView.mj_header endRefreshing];

        // 判断有没有更多的数据
        if(self.shops.count >= [responseObject[@"info"][@"count"] integerValue])
        {
            [self.collectionView.mj_footer setState:MJRefreshStateNoMoreData];
        }else
        {
            [self.collectionView.mj_footer setState:MJRefreshStateIdle];
        }

    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        if (self.params != params) return ;

        //请求数据失败后, 提示用户
        [SVProgressHUD showErrorWithStatus:@"加载新数据失败"];

        // 结束刷新
        [self.collectionView.mj_header endRefreshing];

    }];


}

- (void)footerPull
{
    // 防止用户快速上下拖拽, 造成上下的视图都在刷新
    if(self.collectionView.mj_header.isRefreshing) [self.collectionView.mj_header endRefreshing];


    NSMutableDictionary *params = [NSMutableDictionary dictionary];

    params[@"a"] = @"list";
    params[@"c"] = @"data";
    params[@"type"] = LMJTopicType;
    // 第一次加载帖子时候不需要传入此关键字，当需要加载下一页时：需要传入加载上一页时返回值字段“maxtime”中的内容
    params[@"maxtime"] = self.maxtime;
    //    当前所加载的页码数，默认为0
    params[@"page"] = @(self.page + 1);

    // 记录操作
    self.params = params;


    [self.manager GET:LMJBaiSiJieHTTPAPI parameters:params progress:^(NSProgress * _Nonnull downloadProgress) {
        if(self.params != params) return ;


    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        if(self.params != params) return ;

        // 添加数据
        [self.shops addObjectsFromArray:[LMJShop mj_objectArrayWithKeyValuesArray:responseObject[@"list"]]];

        // 记录页码
        self.page += 1;

        // 记录需要加载下一页的数据
        self.maxtime = responseObject[@"info"][@"maxtime"];


        // 刷新collection
        [self.collectionView reloadData];

        // 结束尾部的刷新
        [self.collectionView.mj_footer endRefreshing];

        // 判断数据是否加载完毕
        if(self.shops.count >= [responseObject[@"info"][@"count"] integerValue])
        {
            [self.collectionView.mj_footer setState:MJRefreshStateNoMoreData];
        }else
        {
            [self.collectionView.mj_footer setState:MJRefreshStateIdle];
        }

    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        if(self.params != params) return ;

        // 加载更多数据失败
        [SVProgressHUD showErrorWithStatus:@"加载更多数据失败"];

        [self.collectionView.mj_footer endRefreshing];

    }];


}
- (void)dealloc
{
    self.params = nil;
    [self.manager.operationQueue cancelAllOperations];//?
}

```
