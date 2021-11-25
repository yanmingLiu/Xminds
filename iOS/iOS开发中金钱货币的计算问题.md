ios开发中涉及到和货币价格计算相关的，都需要注意计算精度的问题。
服务器有可能返回的金钱是int float double string NSNumber, 单纯的float、double的计算都会出现精度问题，即使只是两位小数，也会出现误差。
苹果为我们提供了NSDecimalNumber的API来处理金钱的计算的问题：
    1、字符串转float等不精确问题。
    2、精确计算
    3、保留小数位数
    4、四舍五入及其它的保留小数位数规则

###NSDecimalNumber的使用

**1.把相应的类型转换成NSDecimalNumber类型 **
```objc
    //1、NSString -> NSDecimalNumber
    NSDecimalNumber *num1 = [NSDecimalNumber decimalNumberWithString:@"100"];
    NSLog(@"%@",num1);
    
    //2、NSNumber -> NSDecimalNumber(基础类型的话，先转成NSNumber)
    NSDecimalNumber *num2 = [NSDecimalNumber decimalNumberWithDecimal:[[NSNumber numberWithInt:50] decimalValue]];
    NSLog(@"%@",num2);
```

**2.相关运算**
加+
```
/*加 +
     - (NSDecimalNumber *)decimalNumberByAdding:(NSDecimalNumber *)decimalNumber;
     - (NSDecimalNumber *)decimalNumberByAdding:(NSDecimalNumber *)decimalNumber withBehavior:(nullable id <NSDecimalNumberBehaviors>)behavior;//带保留小数位数
     */
    NSDecimalNumber *num3 = [num1 decimalNumberByAdding:num2];
    NSLog(@"num3 == %@",num3);
```
减 -
```
    /*减 -
     - (NSDecimalNumber *)decimalNumberBySubtracting:(NSDecimalNumber *)decimalNumber;
     - (NSDecimalNumber *)decimalNumberBySubtracting:(NSDecimalNumber *)decimalNumber withBehavior:(nullable id <NSDecimalNumberBehaviors>)behavior;//带保留小数位数
     */
    NSDecimalNumber *num4 = [num1 decimalNumberBySubtracting:num2];
    NSLog(@"num4 == %@",num4);
```
乘 *
```
    /*乘 *
     - (NSDecimalNumber *)decimalNumberByMultiplyingBy:(NSDecimalNumber *)decimalNumber;
     - (NSDecimalNumber *)decimalNumberByMultiplyingBy:(NSDecimalNumber *)decimalNumber withBehavior:(nullable id <NSDecimalNumberBehaviors>)behavior;//带保留小数位数
     */
    NSDecimalNumber *num5 = [num1 decimalNumberByMultiplyingBy:num2];
    NSLog(@"num5 == %@",num5);
```
除 \
```
    /*除 \
     - (NSDecimalNumber *)decimalNumberByDividingBy:(NSDecimalNumber *)decimalNumber;
     - (NSDecimalNumber *)decimalNumberByDividingBy:(NSDecimalNumber *)decimalNumber withBehavior:(nullable id <NSDecimalNumberBehaviors>)behavior;//带保留小数位数
     */
    NSDecimalNumber *num6 = [num1 decimalNumberByDividingBy:num2];
    NSLog(@"num6 == %@",num6);
```
比较
```
    /*比较
     - (NSComparisonResult)compare:(NSNumber *)decimalNumber;
     */
    NSComparisonResult result = [num1 compare:num2];
    NSLog(@"result == %ld",(long)result);
```

保留小数及相关参数含义
```
 // 保留小数
+ (instancetype)decimalNumberHandlerWithRoundingMode:(NSRoundingMode)roundingMode scale:(short)scale raiseOnExactness:(BOOL)exact raiseOnOverflow:(BOOL)overflow raiseOnUnderflow:(BOOL)underflow raiseOnDivideByZero:(BOOL)divideByZero;

//1、 NSRoundingMode
    // Rounding policies :
    // Original
    //    value 1.2  1.21  1.25  1.35  1.27
    // Plain    1.2  1.2   1.3   1.4   1.3
    // Down     1.2  1.2   1.2   1.3   1.2
    // Up       1.2  1.3   1.3   1.4   1.3
    // Bankers  1.2  1.2   1.2   1.4   1.3
    
    /* typedef NS_ENUM(NSUInteger, NSRoundingMode) {
     NSRoundPlain,   // Round up on a tie        四舍五入
     NSRoundDown,    // Always down == truncate  向下保留
     NSRoundUp,      // Always up                向上保留
     NSRoundBankers  // on a tie round so last digit is even 貌似是：
     
     if(四舍五入位 == 5)（四舍五入位（5）+  保留位 ）%2 == 0 ？ 入 ： 舍 ；
     
     if(四舍五入位 ！= 5) 遵从四舍五入
     };
     */
    //2、scale 保留小数个数
    //3、其它：溢出等是否报错

```
```
    NSDecimalNumber *num_1 = [NSDecimalNumber decimalNumberWithString:@"1.2"];
    NSDecimalNumber *num_2 = [NSDecimalNumber decimalNumberWithString:@"3.1415926"];
    
    NSDecimalNumberHandler *handel = [NSDecimalNumberHandler decimalNumberHandlerWithRoundingMode:NSRoundBankers scale:2 raiseOnExactness:NO raiseOnOverflow:NO raiseOnUnderflow:NO raiseOnDivideByZero:NO];
        
    NSDecimalNumber *num_3 = [num_1 decimalNumberByMultiplyingBy:num_2 withBehavior:handel];
    NSLog(@"num_3 == %@",num_3);
```

**3.对金钱进行千分位和保留两位小数处理**

3.1使用NSNumberFormatter去格式化
```
    NSNumberFormatter *formatter = [[NSNumberFormatter alloc] init];
    formatter.numberStyle = kCFNumberFormatterCurrencyAccountingStyle;
    NSString *newAmount = [formatter stringFromNumber:[NSNumber numberWithLong:[@"10000" integerValue]]];
    NSLog(@"newAmount==%@",newAmount);

/*
typedef CF_ENUM(CFIndex, CFNumberFormatterStyle) {	// number format styles
	kCFNumberFormatterNoStyle = 0,
	kCFNumberFormatterDecimalStyle = 1,
	kCFNumberFormatterCurrencyStyle = 2,
	kCFNumberFormatterPercentStyle = 3,
	kCFNumberFormatterScientificStyle = 4,
	kCFNumberFormatterSpellOutStyle = 5,
	kCFNumberFormatterOrdinalStyle CF_ENUM_AVAILABLE(10_11, 9_0) = 6,
	kCFNumberFormatterCurrencyISOCodeStyle CF_ENUM_AVAILABLE(10_11, 9_0) = 8,
	kCFNumberFormatterCurrencyPluralStyle CF_ENUM_AVAILABLE(10_11, 9_0) = 9,
	kCFNumberFormatterCurrencyAccountingStyle CF_ENUM_AVAILABLE(10_11, 9_0) = 10,
};
*/
```
3.2 - 工作中常用的工具
![WX20190822-190112@2x.png](https://upload-images.jianshu.io/upload_images/2051176-81e1a42ad0d00ca4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1024)

 ```

/// 处理json float double 精度丢失问题
- (NSString *)ym_decimalNumber {
    NSString *doubleString  = [NSString stringWithFormat:@"%f", self.doubleValue];
    NSDecimalNumber *decNumber = [NSDecimalNumber decimalNumberWithString:doubleString];
    return [decNumber stringValue];
}
```

```

/// 金钱显示 100,000.00
+ (NSString *)ym_formatDecimalNumber:(NSString *)string {
    if (!string || string.length == 0) {
        return string;
    }

    NSNumber *number = @([string doubleValue]);
    NSNumberFormatter *formatter = [[NSNumberFormatter alloc] init];
    formatter.numberStyle = kCFNumberFormatterDecimalStyle;
    formatter.positiveFormat = @"###,##0.00";

    NSString *amountString = [formatter stringFromNumber:number];
    return amountString;
}

```

```

/**
 金钱显示 0.00 小数点后2位 & 符号 变小
 */
+ (NSAttributedString *)ym_priceString:(NSString *)text textColor:(UIColor *)textColor smallFont:(UIFont *)smallFont bigFont:(UIFont *)bigFont symbol:(NSString *)symbol
{
    if (!text || text.length == 0) {
        text = @"0.00";
    }
    NSRange range = [text rangeOfString:@"."];

    if (range.length == 0) {
        return [[NSAttributedString alloc] initWithString:@""];
    }

    NSArray *texts = [text componentsSeparatedByString:@"."];

    NSMutableAttributedString *mStr = [[NSMutableAttributedString alloc]initWithString:symbol.length ? symbol : @"" attributes:@{NSFontAttributeName:smallFont,NSForegroundColorAttributeName:textColor}];

    NSAttributedString *bigStr = [[NSAttributedString alloc]initWithString:texts[0] attributes:@{NSFontAttributeName:bigFont,NSForegroundColorAttributeName:textColor}];

    NSAttributedString *pointStr = [[NSAttributedString alloc]initWithString:@"." attributes:@{NSFontAttributeName:smallFont,NSForegroundColorAttributeName:textColor}];

    NSAttributedString *smallStr = [[NSAttributedString alloc]initWithString:texts[1] attributes:@{NSFontAttributeName:smallFont,NSForegroundColorAttributeName:textColor}];

    [mStr appendAttributedString:bigStr];
    [mStr appendAttributedString:pointStr];
    [mStr appendAttributedString:smallStr];
    return mStr;
}

```


```
/**
 * 中划线
 */
+ (NSAttributedString *)ym_strikethroughString:(NSString *)text textColor:(UIColor *)textColor font:(UIFont *)font symbol:(NSString *)symbol
{
    if (!text || text.length == 0) {
        return [[NSAttributedString alloc] initWithString:@""];
    }
    NSDictionary *dic = @{NSFontAttributeName:font,NSForegroundColorAttributeName:textColor,NSStrikethroughStyleAttributeName: [NSNumber numberWithInt:NSUnderlineStyleSingle]};

    NSAttributedString *astr = [[NSAttributedString alloc] initWithString:[NSString stringWithFormat:@"%@%@", symbol,text] attributes:dic];
    return astr;
}

```
