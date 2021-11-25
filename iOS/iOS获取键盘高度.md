[demo1](https://github.com/yanmingLiu/RACDemo)
[demo2](https://github.com/yanmingLiu/TextExpan)

```

- (void)addNoticeForKeyboard {
    //监听弹出键盘
    @weakify(self);
    [[[NSNotificationCenter defaultCenter] rac_addObserverForName:UIKeyboardWillShowNotification object:nil] subscribeNext:^(NSNotification * _Nullable noti) {
        @strongify(self);

        NSDictionary *userInfo = [noti userInfo];
        NSTimeInterval animationDuration;
        UIViewAnimationCurve animationCurve;
        CGRect keyboardBeginFrame;
        CGRect keyboardEndFrame;
        [[userInfo objectForKey:UIKeyboardFrameEndUserInfoKey] getValue:&keyboardEndFrame];
        [[userInfo objectForKey:UIKeyboardFrameBeginUserInfoKey] getValue:&keyboardBeginFrame];
        [[userInfo objectForKey:UIKeyboardAnimationCurveUserInfoKey] getValue:&animationCurve];
        [[userInfo objectForKey:UIKeyboardAnimationDurationUserInfoKey] getValue:&animationDuration];

        CGFloat offsetHeight = self.view.height - keyboardEndFrame.origin.y - kTabBarMargin;
        offsetHeight = offsetHeight > 0 ? offsetHeight : 0;
        
        NSLog(@"willShowNotification : %f\n, keyboardBeginFrame = %@\n,keyboardEndFrame = %@", offsetHeight,NSStringFromCGRect(keyboardBeginFrame),NSStringFromCGRect(keyboardEndFrame));
        
        [UIView animateWithDuration:animationDuration animations:^{
            [UIView setAnimationCurve:animationCurve];
            self.textField.y = self.view.height - 40 - kTabBarMargin;
            [self.view layoutIfNeeded];
        }];

    }];

    //可以监听收回键盘
    [[[NSNotificationCenter defaultCenter] rac_addObserverForName:UIKeyboardWillHideNotification object:nil] subscribeNext:^(NSNotification * _Nullable noti) {
        @strongify(self);

        NSDictionary *userInfo = [noti userInfo];
        NSTimeInterval animationDuration;
        UIViewAnimationCurve animationCurve;
        [[userInfo objectForKey:UIKeyboardAnimationCurveUserInfoKey] getValue:&animationCurve];
        [[userInfo objectForKey:UIKeyboardAnimationDurationUserInfoKey] getValue:&animationDuration];
        
        CGFloat y = self.view.bounds.size.height - 40 - kNavigationH - kTabBarMargin;
        NSLog(@"willShowNotification : %f", y);

        [UIView animateWithDuration:animationDuration animations:^{
            [UIView setAnimationCurve:animationCurve];
            self.textField.y = y;
            [self.view layoutIfNeeded];
        }];

    }];
}
```
