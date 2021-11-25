最后效果图
![WX20190726-214215@2x.png](https://upload-images.jianshu.io/upload_images/2051176-ec16e50cae14ef8b.png?imageMogr2/auto-orient/strip%7CimageView2/0/w/180)


坑：
1.**TXIMSDK_TUIKit_iOS**中TContactViewModel字母分组的问题，如果好友信息和自己服务器管理的挂钩 那就直接拉取好友信息

```ObjectiveC
/**
 * 【模块名称】消息列表视图模型（TContactViewModel）
 * 【功能说明】实现消息列表视图模型。
 *  本视图模型负责从服务器拉取好友列表、好友请求并将相关数据加载。
 *  同时本视图模型还会将好友按昵称首字母进行分组，从而帮助视图在界面右侧维护一个“字母表”方便快速检索好友。
 */
@interface TContactViewModel : NSObject


```

整个tableview代码如下
```ObjectiveC

#import "TUIConversationListController.h"
#import "TCommonContactCellData.h"

#import "NSString+Common.h" // TIM排序

@interface TUIContactActionCellData : TCommonCellData

@property NSString *title;
@property UIImage *icon;
@property NSInteger redNum;

@end

```

```ObjectiveC

@implementation TUIContactActionCellData

@end

@interface ContactListView()<UITableViewDelegate, UITableViewDataSource>

// 复制viewModel里面的代码
@property (strong) NSDictionary<NSString *, NSArray<TCommonContactCellData *> *> *dataDict;
@property (strong) NSArray *groupList;
@property (assign) BOOL isLoadFinished;

@end

@implementation ContactListView

- (void)viewDidLoad{
    [super viewDidLoad];
 
    self.tableView = [[UITableView] alloc] initWithFrame:self.view.bounds style:UITableViewStylePlain];
    self.tableView.delegate = self;
    self.tableView.dataSource = self;
    [self.tableView registerNib:[UINib nibWithNibName:@"cell" bundle:nil] forCellReuseIdentifier:@"cell"];
    [self.tableView setSeparatorStyle:UITableViewCellSeparatorStyleNone];
    self.tableView.rowHeight = 80;
    self.tableView.sectionHeaderHeight = 30;
    self.tableView.sectionFooterHeight = 0;
    self.tableView.sectionIndexColor = RGB_COLOR(72, 72, 72);
    [self addSubview:self.tableView];

    // 监听消息
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(reloadData) name:TUIKitNotification_onAddFriends object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(reloadData) name:TUIKitNotification_onDelFriends object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(reloadData) name:TUIKitNotification_onFriendProfileUpdate object:nil];

    [self reloadData];
}

#pragma mark - UITableViewDelegate, UITableViewDataSource


- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView;
{
    return self.groupList.count;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    NSString *group = self.groupList[section];
    NSArray *list = self.dataDict[group];

    NSInteger count = list.count;
    self.tableView.noContentView.hidden = count;
    return count;
}

- (UIView *)tableView:(UITableView *)tableView viewForHeaderInSection:(NSInteger)section
{
    static NSString *headerViewId = @"ContactDrawerView";
    UITableViewHeaderFooterView *headerView = [tableView dequeueReusableHeaderFooterViewWithIdentifier:headerViewId];

    if (!headerView) {
        headerView = [[UITableViewHeaderFooterView alloc] initWithReuseIdentifier:headerViewId];
        headerView.contentView.backgroundColor = [UIColor whiteColor];

        UILabel *textLabel = [[UILabel alloc] initWithFrame:CGRectMake(25, 0, tableView.width - 25, 30)];
        textLabel.tag = 111;
        textLabel.backgroundColor = [UIColor whiteColor];
        textLabel.font = [UIFont boldSystemFontOfSize:17];
        textLabel.textColor = RGB_COLOR(54, 54, 54);
        [headerView addSubview:textLabel];
    }
    UILabel *label = [headerView viewWithTag:111];
    label.text = self.groupList[section];
    return headerView;
}

- (NSArray *)sectionIndexTitlesForTableView:(UITableView *)tableView {
    NSMutableArray *array = [NSMutableArray arrayWithObject:@""];
    [array addObjectsFromArray:self.groupList];
    return array;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"cell" forIndexPath:indexPath];

    NSString *group = self.groupList[indexPath.section];
    NSArray *list = self.dataDict[group];
    TCommonContactCellData *data = list[indexPath.row];

    // 刷新cell
    TIMUserProfile *friend = data.friendProfile.profile;
    [cell reloadCellWithTIMUserProfile:friend];

    return cell;
}

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath{

    NSString *group = self.groupList[indexPath.section];
    NSArray *list = self.dataDict[group];
    TCommonContactCellData *data = list[indexPath.row];

    if ([data.identifier isEqualToString:[IBGlobalManager shareInstance].userInfo._id]) {
        return;
    }
    // 跳转聊天界面
    [ChatRoute chatWithType:TIM_C2C nickname:data.title convId:data.identifier];
}


- (void)loadContacts
{
    self.isLoadFinished = NO;

    [[TIMFriendshipManager sharedInstance] getFriendList:^(NSArray<TIMFriend *> *friends) {

        NSMutableDictionary *dataDict = @{}.mutableCopy;
        NSMutableArray *groupList = @[].mutableCopy;
        NSMutableArray *nonameList = @[].mutableCopy;

        for (TIMFriend *friend in friends) {
            TCommonContactCellData *data = [[TCommonContactCellData alloc] initWithFriend:friend];
            // 就是这里，需要和demo里面的排序字段不一样
            data.title = friend.profile.nickname;

            NSString *group = [[data.title firstPinYin] uppercaseString];
            if (group.length == 0 || !isalpha([group characterAtIndex:0])) {
                [nonameList addObject:data];
                continue;
            }
            NSMutableArray *list = [dataDict objectForKey:group];
            if (!list) {
                list = @[].mutableCopy;
                dataDict[group] = list;
                [groupList addObject:group];
            }
            [list addObject:data];
        }

        [groupList sortUsingSelector:@selector(localizedStandardCompare:)];
        if (nonameList.count) {
            [groupList addObject:@"#"];
            dataDict[@"#"] = nonameList;
        }
        for (NSMutableArray *list in [dataDict allValues]) {
            [list sortUsingSelector:@selector(compare:)];
        }

        self.groupList = groupList;
        self.dataDict = dataDict;
        self.isLoadFinished = YES;

        [self.tableView reloadData];
        [self.tableView.mj_header endRefreshing];

    } fail:nil];
}

- (void)reloadData {
    [self loadContacts];
}

```

2.在点击好友，进入聊天界面的时候，
不要直接获取会话，直接获取会创建一新的会话，
会话列表会都一条没有消息的会话。
应该在聊天界面判断会话列表是否有该会话，
再发送消息的时候没有会话再去获取
```ObjectiveC

@interface ChatViewController : UIViewController

@property (strong, nonatomic) NSString *nickname;
@property (strong, nonatomic) NSString *convId;
@property (nonatomic, assign) TIMConversationType type;

@end



- (void)loadData {

    self.navigationView.titleLabel.text = self.nickname;

    if (self.type == TIM_GROUP) {
        self.conv = [[TIMManager sharedInstance] getConversation:TIM_GROUP receiver:self.convId];
        // 获取群信息
        [self getGroupInfo];
        // 获取消息
        [self loadMessage];
    }
    else {
        // [self.viewModel loadConversation];
        for (TUIConversationCellData *data in self.viewModel.dataList) {
            if ([data.convId isEqualToString:self.convId]) {
                self.conv = [[TIMManager sharedInstance] getConversation:data.convType receiver:data.convId];
                // 获取消息
                [self loadMessage];
                break;
            }
        }
    }
}
```
