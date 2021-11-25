这个效果两个点就搞定：
1.headerView的图片模式为UIViewContentModeScaleAspectFill
2.监听scrollView滚动改变图片的高度

```
/* 状态栏高度 */
#define kStatusBarH     CGRectGetHeight([UIApplication sharedApplication].statusBarFrame)
/* NavBar高度 */
#define kNavigationBarH CGRectGetHeight(self.navigationController.navigationBar.frame)
/* 导航栏 高度 */
#define kNavigationH   (kStatusBarH + kNavigationBarH)

static CGFloat headerH = 200;


- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    
    [self.navigationController setNavigationBarHidden:YES animated:animated];
    
    if (@available(iOS 11.0, *)) {
        [[UIScrollView appearance] setContentInsetAdjustmentBehavior:UIScrollViewContentInsetAdjustmentNever];
    }
    
    self.statusBarStyle = UIStatusBarStyleLightContent;
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
    
    [self.navigationController setNavigationBarHidden:NO animated:animated];
}

- (void)viewDidLoad {
    [super viewDidLoad];
    
    [self.view addSubview:self.tableView];
    
    self.tableView.contentInset = UIEdgeInsetsMake(headerH, 0, 0, 0 );
    // 设置滚动指示器的位置
    self.tableView.scrollIndicatorInsets = self.tableView.contentInset;
    
    self.headerView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, self.view.bounds.size.width, headerH)];
    self.headerView.backgroundColor = [UIColor colorWithRed:247.00/255 green:247.00/255 blue:247.00/255 alpha:1];
    [self.view addSubview:self.headerView];
    
    self.headerImageView = [[UIImageView alloc] initWithFrame:self.headerView.bounds];
    self.headerImageView.clipsToBounds = YES;
    self.headerImageView.contentMode = UIViewContentModeScaleAspectFill;
    self.headerImageView.image = [UIImage imageNamed:@"10"];
    
    [self.headerView addSubview:self.headerImageView];
    
}

- (UIStatusBarStyle)preferredStatusBarStyle {
    return self.statusBarStyle;
}

- (void)scrollViewDidScroll:(UIScrollView *)scrollView  {
    
    CGFloat offset = scrollView.contentOffset.y + scrollView.contentInset.top;

    if (offset <= 0) { // 下拉

        self.headerView.y = 0;
        self.headerView.height = headerH - offset;
        self.headerImageView.alpha = 1;
        
    }else { // 上拉
        
        self.headerView.height = headerH;

        // 设置headerView 最小Y值 模拟导航栏
        CGFloat minY = headerH - kNavigationH;
        self.headerView.y = -MIN(minY,offset);
        
        // 设置透明度
        CGFloat scale = 1 - (offset / minY);
        self.headerImageView.alpha = scale;
        
        // 设置状态栏
        self.statusBarStyle = scale < 0.5 ? UIStatusBarStyleDefault : UIStatusBarStyleLightContent;
        [self.navigationController setNeedsStatusBarAppearanceUpdate];
    }
    
    self.headerImageView.height = self.headerView.height;
}


- (UITableView *)tableView {
    if (!_tableView) {
        _tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:(UITableViewStylePlain)];
        _tableView.dataSource = self;
        _tableView.delegate = self;
        
        [_tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:cellId];
    }
    return _tableView;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return 20;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath  {
    
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellId forIndexPath:indexPath];
    
    cell.textLabel.text = @(indexPath.row).stringValue;
    
    return cell;
}
```
