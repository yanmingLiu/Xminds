###一、 标签在前：
如果给富文本添加attachment，那如果后面的文字是连着的当字符（譬如98798798...）就会出现attachment和文字换行的问题，字符会另起一行。

遇到这种情况就要利用富文本的首行缩进功能，让首行空出图片的位子，图片的宽高 = titleFont.lineHeight
[demo- TitleTagViewController](https://github.com/yanmingLiu/RxSwiftWorkspace/blob/master/RxSwiftWorkspace/RxSwiftWorkspace/Modules/UIList/TitleTagViewController.swift)

![image.png](https://upload-images.jianshu.io/upload_images/2051176-535217ccfa987d11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```swift+
        let titleLabel = UILabel()
        titleLabel.backgroundColor = .random
        titleLabel.numberOfLines = 2
        view.addSubview(titleLabel)
        titleLabel.translatesAutoresizingMaskIntoConstraints = false
        titleLabel.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20).isActive = true
        titleLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -20).isActive = true
        titleLabel.topAnchor.constraint(equalTo: label1.bottomAnchor, constant: 40).isActive = true

        // 标签图片
        let imgv = UIImageView(image: UIImage(named: "iconVideoSelected"))
        view.addSubview(imgv)
        imgv.translatesAutoresizingMaskIntoConstraints = false
        imgv.leftAnchor.constraint(equalTo: titleLabel.leftAnchor).isActive = true
        imgv.topAnchor.constraint(equalTo: titleLabel.topAnchor).isActive = true
        
        // tittle
        let font = UIFont.boldSystemFont(ofSize: 18)
        let paragraph = NSMutableParagraphStyle()
        paragraph.lineSpacing = 4
        paragraph.lineBreakMode = .byTruncatingTail
        paragraph.firstLineHeadIndent = font.lineHeight + 4
        let attributes: [NSAttributedString.Key: Any] = [.font: font, .foregroundColor: UIColor.black, .paragraphStyle: paragraph]
        let title = "132413412341234123412341234123412341233412341234123412341234123423412341234123412341234"
        let attrText = NSAttributedString(string: title, attributes: attributes)
        titleLabel.attributedText = attrText

```


###二、标签在后：
如果标签在标题后面，就需要考虑到标题换行的问题。这里用YYKit处理的。

效果如下：
![BHgTA T 2018-11-22 143260.jpg](https://upload-images.jianshu.io/upload_images/2051176-4a8121d7a6272d33.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
    NSMutableAttributedString *mStr = [NSMutableAttributedString new];

    // 设置tag标签
    NSString *tagStr = [NSString stringWithFormat:@" %@ ", model.cate_name];
    NSMutableAttributedString *mTagStr = [[NSMutableAttributedString alloc] initWithString:tagStr];
    mTagStr.yy_font = FONT(9);
    mTagStr.yy_color = ColorHex(0xF9860A);

    YYTextBorder *border = [YYTextBorder new];
    border.strokeColor = ColorHex(0xF9860A);
    border.strokeWidth = 1;
    border.lineStyle = YYTextLineStyleSingle;
    border.cornerRadius = 2;
    border.insets = UIEdgeInsetsMake(0, 1, 0, 1);
    mTagStr.yy_textBorder = border;

    [mStr appendAttributedString:name];

    // 计算tag标签文字宽度
    CGSize size = [tagStr ym_sizeWithFont:FONT(9) maxWidth:160];

    // 新建一个tagv 用于生成图片
    YYLabel *tagV = [YYLabel new];
    tagV.attributedText = mTagStr;
    tagV.width = size.width;
    tagV.height = 20;

    // 生成图片
    UIImage *image = [UIImage getmakeImageWithView:tagV andWithSize:tagV.frame.size];

    // 生成附件
    NSMutableAttributedString *attachText = [NSMutableAttributedString yy_attachmentStringWithContent:image contentMode:UIViewContentModeCenter attachmentSize:image.size alignToFont:FONT(9) alignment:YYTextVerticalAlignmentCenter];

    // 拼接文本
    [mStr appendAttributedString:attachText];

    self.yyNameLabel.attributedText = mStr;
```
