---
title: CalculatorGUI计算器基础实现
categories: [iOS]
tags: [iOS,移动开发,Objective-C]
copyright: true
comments: true
top: false
author: XQ
mathjax: false
date: 2018-12-21 10:00:25
updated: 2018-12-20 23:03:25
keywords: iOS,移动开发,Objective-C
description: iOS应用开发入门，持续更新，教训——慎重命名
passwords: 
img:

---

``` objectivec
//
//  NSObject+Calculator.h
//  CalculaterGUI
//
//  Created by XQ on 2018/12/10.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import <Foundation/Foundation.h>



@interface Calculator : NSObject
@property (strong ,nonatomic) NSMutableString * string;
- (void) deleteNumber;
- (NSString *) returnResult;
- (void) clearString;
@end
```

``` objectivec
//
//  NSObject+Calculator.m
//  CalculaterGUI
//
//  Created by XQ on 2018/12/10.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "Calculator.h"

@implementation Calculator
- (NSMutableString *) string{
    if(!_string){
        _string = [[NSMutableString alloc] init];
    }
    return _string;
}
/*删除数字*/
- (void)deleteNumber{
    long length = self.string.length - 1;
    if(length >= 0){
        [self.string deleteCharactersInRange:NSMakeRange(length, 1)];
    }
}

/*清空*/
- (void)clearString{
    self.string = nil;
}

/*返回结果*/
- (NSString *)returnResult{
    @try {
        NSExpression * exp = [NSExpression expressionWithFormat:self.string];
        id value = [exp expressionValueWithObject:nil context:nil];
        NSLog(@"Result=%f",[value floatValue]);
        self.string = [NSMutableString stringWithString:[value stringValue]];
        return [value stringValue];
    } @catch (NSException *exception) {
        self.string = nil;
        return @"Error";
    } 
    
    
}
@end

```

``` objectivec
//
//  ViewController.m
//  CalculaterGUI
//
//  Created by XQ on 2018/12/9.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "ViewController.h"
#import "Model/Calculator.h"
@interface ViewController ()
{Boolean flag;}
@property (weak, nonatomic) IBOutlet UIImageView *backGround;
@property (weak, nonatomic) IBOutlet UITextField *TextField;
@property (weak, nonatomic) IBOutlet UITextField *lastText;

@property (weak, nonatomic) IBOutlet UIButton *Button1;
@property (weak, nonatomic) IBOutlet UIButton *Button2;
@property (weak, nonatomic) IBOutlet UIButton *Button3;
@property (weak, nonatomic) IBOutlet UIButton *Button4;
@property (weak, nonatomic) IBOutlet UIButton *Button5;
@property (weak, nonatomic) IBOutlet UIButton *Button6;
@property (weak, nonatomic) IBOutlet UIButton *Button7;
@property (weak, nonatomic) IBOutlet UIButton *Button8;
@property (weak, nonatomic) IBOutlet UIButton *Button9;
@property (weak, nonatomic) IBOutlet UIButton *Button0;

@property (weak, nonatomic) IBOutlet UIButton *Buttondot;

@property (weak, nonatomic) IBOutlet UIButton *Buttonadd;
@property (weak, nonatomic) IBOutlet UIButton *Buttonsub;
@property (weak, nonatomic) IBOutlet UIButton *Buttonmul;
@property (weak, nonatomic) IBOutlet UIButton *Buttondiv;

@property (weak, nonatomic) IBOutlet UIButton *Buttonclear;
@property (weak, nonatomic) IBOutlet UIButton *Buttondelete;
@property (weak, nonatomic) IBOutlet UIButton *Buttonreverse;

@property (strong,nonatomic) Calculator * calculator;


@end

@implementation ViewController
//https://blog.csdn.net/u013775224/article/details/78642089
- (UIStatusBarStyle)preferredStatusBarStyle {
    return UIStatusBarStyleLightContent;
}
- (void)viewWillAppear:(BOOL)animated{
    [super viewWillAppear:animated];
    self.navigationController.navigationBar.hidden = YES;
    self.navigationController.navigationBar.barStyle = UIStatusBarStyleLightContent;
    
    UIToolbar * uitoolbar = [[UIToolbar alloc] init];
    uitoolbar.frame = self.backGround.bounds;
    uitoolbar.barStyle = UIBarStyleBlack;
    uitoolbar.alpha = 0.99;
    [self.backGround addSubview:uitoolbar];
}

//
- (void)viewDidLoad {
    [super viewDidLoad];
    flag = NO;
    // Do any additional setup after loading the view, typically from a nib.
   
    
}
/*输入*/
- (IBAction)inputNumber:(UIButton *)sender {
    NSMutableString * string = [NSMutableString stringWithString:self.TextField.text];
    
    //修复bug
    if(flag == YES){
        NSString * a = [[sender titleLabel] text];
        if([a isEqual:@"+"] || [a isEqual:@"-"] || [a isEqual:@"*"] || [a isEqual:@"/"]){
            [string appendString:[[sender titleLabel] text]];
            [self.calculator.string appendString:[[sender titleLabel] text]];
            self.TextField.text = string;
            flag = NO;
        }
       else{
            self.calculator.string = nil;
            [string appendString:[[sender titleLabel] text]];
            [self.calculator.string appendString:[[sender titleLabel] text]];
            self.TextField.text = string;
            flag = NO;
            
        }
    }
    //
    
    else{
        [string appendString:[[sender titleLabel] text]];
        [self.calculator.string appendString:[[sender titleLabel] text]];
        self.TextField.text = string;
    }

}
/*结果*/
- (IBAction)result:(UIButton *)sender {
    
    self.lastText.text = nil;
    self.lastText.text = self .calculator.returnResult;
    if([self.lastText.text  isEqual: @"Error"]){
        //UIAlertView * uialertview = [[UIAlertView alloc] initWithTitle:@"出现错误辣(>_<)" message:@"输入有误，请输入合法表达式" delegate:0 cancelButtonTitle:@"好的😯" otherButtonTitles:nil, nil];
        //[uialertview show];
        UIAlertController * uiAlertController = [UIAlertController alertControllerWithTitle:@"出现错误辣(>_<)" message:@"输入有误，请输入合法表达式" preferredStyle:UIAlertControllerStyleAlert];
        //
        UIAlertAction * uiYesAction = [UIAlertAction actionWithTitle:@"好的👌" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
            NSLog(@"好的👌");
        }];
        /*
        UIAlertAction * uiNoAction = [UIAlertAction actionWithTitle:@"不行🚫" style:UIAlertActionStyleCancel handler:^(UIAlertAction * _Nonnull action) {
            NSLog(@"不行🚫");
  
        }];
         */
        //
        [uiAlertController addAction:uiYesAction];
        //[uiAlertController addAction:uiNoAction];
        //
        [self presentViewController:uiAlertController animated:YES completion:nil];
        
        
        self.lastText.text = nil;
    }
    self.TextField.text = nil;
    flag = YES;
    
    
    
}

/*删除*/
- (IBAction)delete:(UIButton *)sender {
    if(self.TextField.text != nil){
        long length = self.calculator.string.length - 1;
        if(length >= 0){
            [self.calculator.string deleteCharactersInRange:NSMakeRange(length,1)];
            self.TextField.text = self.calculator.string;
        }
    }
    
    //[self.calculator deleteNumber];
}


/*清除*/
- (IBAction)clear:(UIButton *)sender {
    self.TextField.text = nil;
    self.lastText.text = nil;
    [self.calculator clearString];
}
- (Calculator *)calculator{
    if(!_calculator){
        _calculator = [[Calculator alloc] init];
       
    }
     return _calculator;
}
- (IBAction)reverse:(UIButton *)sender {
    NSMutableString * string = [NSMutableString stringWithString:self.TextField.text];
    [self.calculator.string insertString:@"-" atIndex:0];
    [string insertString:@"-" atIndex:0];
    self.TextField.text = string;
}

@end
```

<center>


![](https://pictures-1257961856.cos.ap-shanghai.myqcloud.com/images/blog_images/calculator/2.png)

![](https://pictures-1257961856.cos.ap-shanghai.myqcloud.com/images/blog_images/calculator/1.png)

![](https://pictures-1257961856.cos.ap-shanghai.myqcloud.com/images/blog_images/calculator/3.png)

![](https://pictures-1257961856.cos.ap-shanghai.myqcloud.com/images/blog_images/calculator/4.png)

``` objectivec
//
//  advanceCalculatorViewController.h
//  CalculaterGUI
//
//  Created by XQ on 2018/12/16.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import <UIKit/UIKit.h>
#import "Model/advanceCalculator.h"
NS_ASSUME_NONNULL_BEGIN

@interface advanceCalculatorViewController : UIViewController
@property(strong,nonatomic) advanceCalculator *cal;
@end

NS_ASSUME_NONNULL_END

```



``` objectivec
//
//  advanceCalculatorViewController.m
//  CalculaterGUI
//
//  Created by XQ on 2018/12/16.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "advanceCalculatorViewController.h"

@interface advanceCalculatorViewController ()

@property (weak, nonatomic) IBOutlet UITextField *lastText_428;
@property (weak, nonatomic) IBOutlet UITextField *currentText_428;
@property (weak, nonatomic) IBOutlet UIImageView *advancebg_428;
@property (weak, nonatomic) IBOutlet UIButton *btn_left_428;
@property (weak, nonatomic) IBOutlet UIButton *btn_right_428;
@property (weak, nonatomic) IBOutlet UIButton *btn_e_428;
@property (weak, nonatomic) IBOutlet UIButton *btn_pi_428;

@end

@implementation advanceCalculatorViewController


- (IBAction)inputKey:(UIButton *)sender {
    NSInteger a = sender.tag;
    if(a == 1 || a== 2 || a == 3 || a == 4){
        NSMutableString * str = [NSMutableString stringWithString:self.currentText_428.text];
        if([[[sender titleLabel]text] isEqualToString:@"e"]){
            [self.cal.string appendString:@"2.7182818"];
        }
        else if([[[sender titleLabel]text] isEqualToString:@"pi"]){
            [self.cal.string appendString:@"3.1415926"];
        }
        else {
            [self.cal.string appendString: [[sender titleLabel] text]];
        }
        [str appendString:[[sender titleLabel] text]];
        self.currentText_428.text = str;
        self.cal.screen = str;
    }
    NSString * title = sender.titleLabel.text;
    if([title isEqualToString:@"sqrt"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal sqrt:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"abs"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal abs:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"x1"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal x1:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"x_2"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal x_2:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"x_3"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal x_3:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"ln"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal ln:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"log"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal log:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"sin"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal sin:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"cos"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal cos:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"tan"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal tan:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"asin"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal asin:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"acos"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal acos:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"atan"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal atan:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"sinh"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal sinh:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"cosh"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal cosh:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    if([title isEqualToString:@"tanh"]){
        self.cal.string = [NSMutableString stringWithString:[self.cal tanh:[self.cal returnResult]]];
        self.cal.screen = self.cal.string;
        self.currentText_428.text = self.cal.screen;
    }
    
}




- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
}

/*
#pragma mark - Navigation

// In a storyboard-based application, you will often want to do a little preparation before navigation
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    // Get the new view controller using [segue destinationViewController].
    // Pass the selected object to the new view controller.
}
*/
- (UIStatusBarStyle)preferredStatusBarStyle {
    return UIStatusBarStyleLightContent;
}
- (void)viewWillAppear:(BOOL)animated{
    //self.navigationController.navigationBar.hidden = YES;
    //self.navigationController.navigationBar.barStyle = UIStatusBarStyleLightContent;
    
    UIToolbar * uitoolbar = [[UIToolbar alloc] init];
    uitoolbar.frame = self.advancebg_428.bounds;
    
    uitoolbar.barStyle = UIBarStyleDefault;
    uitoolbar.alpha = 0.99;
    [self.advancebg_428 addSubview:uitoolbar];
    _lastText_428.enabled= NO;
    _currentText_428.enabled = NO;
    
    self.currentText_428.text = self.cal.screen;
    
}


@end

```

``` objectivec
//
//  HealthViewController.h
//  CalculaterGUI
//
//  Created by XQ on 2018/12/20.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN

@interface HealthViewController : UIViewController<UITextFieldDelegate>

@end

NS_ASSUME_NONNULL_END

```

``` objectivec
//
//  HealthViewController.m
//  CalculaterGUI
//
//  Created by XQ on 2018/12/20.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "HealthViewController.h"
#import "Model/Calculator+Health.h"
@interface HealthViewController ()
@property (weak, nonatomic) IBOutlet UITextField *weight_428;
@property (weak, nonatomic) IBOutlet UITextField *height_428;
@property (weak, nonatomic) IBOutlet UITextField *advise_428;
@property (weak, nonatomic) IBOutlet UITextField *score_428;
@property (weak, nonatomic) IBOutlet UIButton *compute_428;
@property(strong,nonatomic) Calculator * cal;
@end

@implementation HealthViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    self.cal = [[Calculator alloc]init];
    self.advise_428.enabled = NO;
    self.score_428.enabled = NO;
    // Do any additional setup after loading the view.
}

/*
#pragma mark - Navigation

// In a storyboard-based application, you will often want to do a little preparation before navigation
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    // Get the new view controller using [segue destinationViewController].
    // Pass the selected object to the new view controller.
}
*/
- (void)viewWillAppear:(BOOL)animated{
    
}
- (Calculator *)cal{
    if(!_cal){
        _cal = [[Calculator alloc]init];
    }
    return _cal;
}
- (BOOL)textFieldShouldReturn:(UITextField *)textField{
    if(textField == self.height_428 || textField == self.weight_428){
        [textField resignFirstResponder];
    }
    return YES;
}
- (IBAction)computeScore:(UIButton *)sender {
    NSString * score = [self.cal computeHealthWithHeight:self.height_428.text withWeight:self.weight_428.text];
    if([score floatValue] < 19){
        self.advise_428.text = @"太瘦了";
    }
    else if([score floatValue] < 25){
        self.advise_428.text = @"身材很好";
    }
    else if([score floatValue] < 30){
        self.advise_428.text = @"肥胖，需要锻炼";
    }
    else {
        self.advise_428.text = @"超重";
    }
    self.score_428.text = score;
}
@end

```

``` objectivec
//
//  advanceCalculator.h
//  CalculaterGUI
//
//  Created by XQ on 2018/12/16.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "Calculator.h"
#import <math.h>
NS_ASSUME_NONNULL_BEGIN

@interface advanceCalculator : Calculator
@property (strong,nonatomic) NSMutableString * screen;
-(NSString *)sqrt:(NSString *) content;
-(NSString *)abs:(NSString *) content;
-(NSString *)x1:(NSString *) content;
-(NSString *)x_2:(NSString *) content;

-(NSString *)x_3:(NSString *) content;
-(NSString *)sin:(NSString *) content;
-(NSString *)cos:(NSString *) content;
-(NSString *)tan:(NSString *) content;

-(NSString *)asin:(NSString *) content;
-(NSString *)acos:(NSString *) content;
-(NSString *)atan:(NSString *) content;
-(NSString *)ln:(NSString *) content;

-(NSString *)sinh:(NSString *) content;
-(NSString *)cosh:(NSString *) content;
-(NSString *)tanh:(NSString *) content;
-(NSString *)log:(NSString *) content;
@end

NS_ASSUME_NONNULL_END

```

``` objectivec
//
//  advanceCalculator.m
//  CalculaterGUI
//
//  Created by XQ on 2018/12/16.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "advanceCalculator.h"

@implementation advanceCalculator



- (NSString *)sqrt:(NSString *)content{
    double d = [content doubleValue];
    double res = sqrt(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
-(NSString *)abs:(NSString *)content{
    double d = [content doubleValue];
    double res = fabs(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)x1:(NSString *)content{
    double d = [content doubleValue];
    double res = 1.0/d;
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)x_2:(NSString *)content{
    double d = [content doubleValue];
    double res = pow(d,2);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)x_3:(NSString *)content{
    double d = [content doubleValue];
    double res = pow(d,3);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)ln:(NSString *)content{
    double d = [content doubleValue];
    double res = log(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)log:(NSString *)content{
    double d = [content doubleValue];
    double res = log10(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}

- (NSString *)sin:(NSString *)content{
    double d = [content doubleValue];
    double res = sin(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)cos:(NSString *)content{
    double d = [content doubleValue];
    double res = cos(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)tan:(NSString *)content{
    double d = [content doubleValue];
    double res = tan(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}

- (NSString *)asin:(NSString *)content{
    double d = [content doubleValue];
    double res = asin(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)acos:(NSString *)content{
    double d = [content doubleValue];
    double res = acos(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)atan:(NSString *)content{
    double d = [content doubleValue];
    double res = atan(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)sinh:(NSString *)content{
    double d = [content doubleValue];
    double res = sinh(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)cosh:(NSString *)content{
    double d = [content doubleValue];
    double res = cosh(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}
- (NSString *)tanh:(NSString *)content{
    double d = [content doubleValue];
    double res = tanh(d);
    NSNumber * n = [[NSNumber alloc]initWithDouble: res];
    return [n stringValue];
}




- (void)deleteNumber{
 
    long length = self.string.length - 1;
    if(length >= 0){
        [self.string deleteCharactersInRange:NSMakeRange(length,1)];
        //[self.screen deleteCharactersInRange:NSMakeRange(length, 1)];
    }
    
}
- (void)clearString{
    self.string = nil;
    self.screen = [NSMutableString stringWithString:@""];
}

@end

```

``` objectivec
//
//  Calculator+Health.h
//  CalculaterGUI
//
//  Created by XQ on 2018/12/20.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "Calculator.h"

NS_ASSUME_NONNULL_BEGIN

@interface Calculator (Health)
- (NSString *)computeHealthWithHeight:(NSString *) height withWeight: (NSString *) weight;
@end

NS_ASSUME_NONNULL_END

```

``` objectivec
//
//  Calculator+Health.m
//  CalculaterGUI
//
//  Created by XQ on 2018/12/20.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "Calculator+Health.h"

@implementation Calculator (Health)
- (NSString *)computeHealthWithHeight:(NSString *) height withWeight: (NSString *) weight{
    NSMutableString * exp;
    exp = [NSMutableString stringWithString:weight];
    [exp appendString:@"/(("];
    [exp appendString:height];
    [exp appendString:@"/100.0)"];
    [exp appendString:@"*("];
    [exp appendString:height];
    [exp appendString:@"/100.0))"];
    self.string = exp;
    return [NSString stringWithFormat:@"%.2f",[[self returnResult]floatValue]];
}
@end

```

``` objectivec
//
//  ViewController.h
//  CalculaterGUI
//
//  Created by XQ on 2018/12/9.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import <UIKit/UIKit.h>
#import "Model/Calculator.h"
#import "Model/advanceCalculator.h"
#import "advanceCalculatorViewController.h"
@interface ViewController : UIViewController


@end


```

``` objectivec
//
//  ViewController.m
//  CalculaterGUI
//
//  Created by XQ on 2018/12/9.
//  Copyright © 2018年 XQ. All rights reserved.
//

#import "ViewController.h"

@interface ViewController ()
{Boolean flag;}
@property (weak, nonatomic) IBOutlet UIImageView *backGround;
@property (weak, nonatomic) IBOutlet UITextField *TextField;
@property (weak, nonatomic) IBOutlet UITextField *lastText;

@property (weak, nonatomic) IBOutlet UIButton *Button1;
@property (weak, nonatomic) IBOutlet UIButton *Button2;
@property (weak, nonatomic) IBOutlet UIButton *Button3;
@property (weak, nonatomic) IBOutlet UIButton *Button4;
@property (weak, nonatomic) IBOutlet UIButton *Button5;
@property (weak, nonatomic) IBOutlet UIButton *Button6;
@property (weak, nonatomic) IBOutlet UIButton *Button7;
@property (weak, nonatomic) IBOutlet UIButton *Button8;
@property (weak, nonatomic) IBOutlet UIButton *Button9;
@property (weak, nonatomic) IBOutlet UIButton *Button0;

@property (weak, nonatomic) IBOutlet UIButton *Buttondot;

@property (weak, nonatomic) IBOutlet UIButton *Buttonadd;
@property (weak, nonatomic) IBOutlet UIButton *Buttonsub;
@property (weak, nonatomic) IBOutlet UIButton *Buttonmul;
@property (weak, nonatomic) IBOutlet UIButton *Buttondiv;

@property (weak, nonatomic) IBOutlet UIButton *Buttonclear;
@property (weak, nonatomic) IBOutlet UIButton *Buttondelete;
@property (weak, nonatomic) IBOutlet UIButton *Buttonreverse;

@property (strong,nonatomic) advanceCalculator * advanceCalculator;


@end

@implementation ViewController
//https://blog.csdn.net/u013775224/article/details/78642089
- (UIStatusBarStyle)preferredStatusBarStyle {
    return UIStatusBarStyleLightContent;
}
- (void)viewWillAppear:(BOOL)animated{
    [super viewWillAppear:animated];
    //self.navigationController.navigationBar.hidden = YES;
    //self.navigationController.navigationBar.barStyle = UIStatusBarStyleLightContent;
    
    UIToolbar * uitoolbar = [[UIToolbar alloc] init];
    uitoolbar.frame = self.backGround.bounds;
    
    uitoolbar.barStyle = UIBarStyleDefault;
    uitoolbar.alpha = 0.99;
    [self.backGround addSubview:uitoolbar];
    
    self.TextField.text = self.advanceCalculator.screen;
    self.advanceCalculator.string = self.advanceCalculator.screen;
    
}

//
- (void)viewDidLoad {
    [super viewDidLoad];
    _TextField.enabled = NO;
    _lastText.enabled = NO;
    flag = NO;
    // Do any additional setup after loading the view, typically from a nib.
   
    
}
/*输入*/
- (IBAction)inputNumber:(UIButton *)sender {
    NSMutableString * string = [NSMutableString stringWithString:self.TextField.text];
    
    //修复bug
    if(flag == YES){
        NSString * a = [[sender titleLabel] text];
        if([a isEqual:@"+"] || [a isEqual:@"-"] || [a isEqual:@"*"] || [a isEqual:@"/"]){
            if([a isEqualToString:@"/"]){
                BOOL flag = false;
                for(int i = 0; i < self.advanceCalculator.screen.length; i++){
                    if([self.advanceCalculator.string characterAtIndex:i] == '.'){
                        flag = true;
                    }
                }
                if(flag == false){
                    [self.advanceCalculator.string appendString:@".0"];
                }
            }
            [string appendString:[[sender titleLabel] text]];
       
            [self.advanceCalculator.string appendString:[[sender titleLabel] text]];
            self.TextField.text = string;
            flag = NO;
        }
       else{
            self.advanceCalculator.string = nil;
           NSString * a = [[sender titleLabel] text];
           if([a isEqualToString:@"/"]){
               BOOL flag = false;
               for(int i = 0; i < self.advanceCalculator.string.length; i++){
                   if([self.advanceCalculator.string characterAtIndex:i] == '.'){
                       flag = true;
                   }
               }
               if(flag == false){
                   [self.advanceCalculator.string appendString:@".0"];
               }
           }
            [string appendString:[[sender titleLabel] text]];
            [self.advanceCalculator.string appendString:[[sender titleLabel] text]];
            self.TextField.text = string;
            flag = NO;
            
        }
    }
    //
    
    else{
        NSString * a = [[sender titleLabel] text];
        if([a isEqualToString:@"/"]){
            BOOL flag = false;
            for(int i = 0; i < self.advanceCalculator.string.length; i++){
                if([self.advanceCalculator.string characterAtIndex:i] == '.'){
                    flag = true;
                }
               
            }
            if(flag == false){
                [self.advanceCalculator.string appendString:@".0"];
            }
            
        }
        [string appendString:[[sender titleLabel] text]];
        [self.advanceCalculator.string appendString:[[sender titleLabel] text]];
        self.TextField.text = string;
    }
    self.advanceCalculator.screen = string;

}
/*结果*/
- (IBAction)result:(UIButton *)sender {
    
    self.lastText.text = nil;
    self.lastText.text = self .advanceCalculator.returnResult;
    if([self.lastText.text  isEqual: @"Error"]){
        //UIAlertView * uialertview = [[UIAlertView alloc] initWithTitle:@"出现错误辣(>_<)" message:@"输入有误，请输入合法表达式" delegate:0 cancelButtonTitle:@"好的😯" otherButtonTitles:nil, nil];
        //[uialertview show];
        UIAlertController * uiAlertController = [UIAlertController alertControllerWithTitle:@"出现错误辣(>_<)" message:@"输入有误，请输入合法表达式" preferredStyle:UIAlertControllerStyleAlert];
        //
        UIAlertAction * uiYesAction = [UIAlertAction actionWithTitle:@"好的👌" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
            NSLog(@"好的👌");
        }];
        /*
        UIAlertAction * uiNoAction = [UIAlertAction actionWithTitle:@"不行🚫" style:UIAlertActionStyleCancel handler:^(UIAlertAction * _Nonnull action) {
            NSLog(@"不行🚫");
  
        }];
         */
        //
        [uiAlertController addAction:uiYesAction];
        //[uiAlertController addAction:uiNoAction];
        //
        [self presentViewController:uiAlertController animated:YES completion:nil];

        self.lastText.text = nil;
    }
    self.TextField.text = nil;
    flag = YES;
    self.advanceCalculator.screen  = [NSMutableString stringWithString: @""];
    [self.advanceCalculator.screen appendString:self.lastText.text];
    
    
}

/*删除*/
- (IBAction)delete:(UIButton *)sender {
    [self.advanceCalculator deleteNumber];
    
     self.TextField.text = self.advanceCalculator.string;
    //[self.advanceCalculator deleteNumber];
}


/*清除*/
- (IBAction)clear:(UIButton *)sender {
    self.TextField.text = nil;
    self.lastText.text = nil;
    [self.advanceCalculator clearString];
}
- (advanceCalculator *)advanceCalculator{
    if(!_advanceCalculator){
        _advanceCalculator = [[advanceCalculator alloc] init];
       
    }
     return _advanceCalculator;
}
- (IBAction)reverse:(UIButton *)sender {
    NSMutableString * string = [NSMutableString stringWithString:self.TextField.text];
    [self.advanceCalculator.string insertString:@"-" atIndex:0];
    [string insertString:@"-" atIndex:0];
    self.TextField.text = string;
}
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender{
    if([segue.identifier isEqualToString:@"advanceScene"]){
        if([segue.destinationViewController isKindOfClass:[advanceCalculatorViewController class] ]){
            advanceCalculatorViewController * acv = (advanceCalculatorViewController *)segue.destinationViewController;
            acv.cal = self.advanceCalculator;
        }
    }
}
- (void)viewDidDisappear:(BOOL)animated{
    self.lastText.text = nil;
}
@end

```

![](https://pictures-1257961856.cos.ap-shanghai.myqcloud.com/images/blog_images/iOS/4.png)

![](https://pictures-1257961856.cos.ap-shanghai.myqcloud.com/images/blog_images/iOS/5.png)

![](https://pictures-1257961856.cos.ap-shanghai.myqcloud.com/images/blog_images/iOS/6.png)