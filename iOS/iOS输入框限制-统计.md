>[iOS常用正则表达式](https://www.jianshu.com/p/13774c6bbdaa)
>[简单完美的解决键盘遮挡输入框的问题]([https://blog.csdn.net/u013928640/article/details/50664250](https://blog.csdn.net/u013928640/article/details/50664250)
)


###1. textField输入金额、数字、整数、小数 输入判断

```
#pragma mark - UITextFieldDelegate
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string {
    
    NSString *toString = [textField.text stringByReplacingCharactersInRange:range withString:string];
    
    if (toString.length) {
        NSString *stringRegex;
        if (textField == self.teamPriceTF || textField == self.masterPriceTF) {
            // 0~99999
            stringRegex = @"(\\+)?(([0]|(0[.]\\d{0,2}))|([1-9]\\d{0,4}(([.]\\d{0,2})?)))?";
        }
        else if (textField == self.prepayPriceTF) {
            // 1~9.9
            stringRegex = @"([1-9]{1}(([.]\\d{0,1})?))?"
        }
        else if (textField == self.personCountTF) {
            // 2~10
            stringRegex = @"[2-9]|10?";
        }
        else if (textField == self.timersTF) {
            // 1~999
            stringRegex = @"[1-9]\\d{0,2}?";
        }
        else if (textField == self.personCountTF) {
            // 2~10
            stringRegex = @"[2-9]|10?";
        }
        else if (textField == self.timersTF) {
            // 1~999
            stringRegex = @"[1-9]\\d{0,2}?";
        }
        NSPredicate *phoneTest = [NSPredicate predicateWithFormat:@"SELF MATCHES %@", stringRegex];
        BOOL flag = [phoneTest evaluateWithObject:toString];
        return flag;
    }
    return YES;
}

```

###2.输入个数限制遇上中文输入法高亮部分的问题 + 输入框视图动态高度
注意点: 

1. 一次性编辑直接用代理、通知就可以监听计算文字字数。
2. 当存在编辑的时候 ，代理、通知在给UITextView赋值的时候，是不被调用的，所以给了一个外部方法再赋值的时候调用。
3. 当textView输入高度变化的时候回调给外面修改视图的高度。


```
    self.inputView.textView.text = _projectDetail.desc;
    [self.inputView textChanged]; // 赋值后调用
```

```
.h
@interface PCInputView : UIView <UITextViewDelegate>

@property (nonatomic, strong) UILabel * nameLabel;
@property (nonatomic, strong) UILabel * countLabel;
@property (nonatomic, strong) IQTextView * textView;

// 限制文字字数
@property (nonatomic, assign) NSInteger maxCount;

// 输入框高度变化后整体的高度
@property (nonatomic, copy) void(^inputHightBlock) (CGFloat curHight);

// 文字改变，计算文字-给外界赋值的时候调用
- (void)textChanged;

@end
```

```
.m
#pragma mark - bindUI

- (void)bindUI {
    // textField 监听输入、计算文字
//    [self.textField addTarget:self action:@selector(editChange:) forControlEvents:UIControlEventEditingChanged];

     // textView 监听输入、计算文字
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(textChangedExt:) name:UITextViewTextDidChangeNotification object:nil];
    // 监听输入框的高度变化
    [self.textView addObserver:self forKeyPath:@"contentSize" options:NSKeyValueObservingOptionNew context:nil];

}

#pragma mark - 监听输入 计算文字

- (void)textChangedExt:(NSNotification *)notification {
    [self textChanged];
}

- (void)textChanged {
    _maxCount = 20;
    NSString *toString = self.textView.text;
    // 获取高亮部分
    UITextRange *selectedRange = [self.textView markedTextRange];
    UITextPosition *position = [self.textView positionFromPosition:selectedRange.start offset:0];
    
    // 判断是否存在高亮字符，如果有，则不进行字数统计和字符串截断
    if (!position) {
        if (toString.length > _maxCount)  {
            self.textView.text = [toString substringToIndex:_maxCount];
        }
        self.countLabel.text = [NSString stringWithFormat:@"%zd/%zd", self.textView.text.length, _maxCount];
    }
}

#pragma mark - kvo 监听输入框的高度变化 并回调外部 修改整体高度

- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context {
    
    UITextView *textView = (UITextView*)object;

    if([keyPath isEqualToString:@"contentSize"]) {
        textView.height = textView.contentSize.height;
        NSLog(@"%f", textView.height);
        !self.inputHightBlock ? : self.inputHightBlock(69+10+textView.height);
    }
}

#pragma mark - textView delegate

- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text {
    
    if ([text isEqualToString:@"\n"]){
        [self endEditing:YES];
        return NO;
    }
    
    return YES;
}

```



###3.去掉首尾空格

```
- (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
    // 将字符串首尾空格去掉
    textField.text = [textField.text  stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];
    return YES;
}
```
