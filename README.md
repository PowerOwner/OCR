

![build_passing](https://img.shields.io/badge/build-passing-green.svg)
![build_version](https://img.shields.io/badge/version-v3.0.0-green.svg)

# 身份证识别 iOS SDK

合合信息 IDCard SDK 是一个轻巧的组件，可以快速轻松地扫描身份证。该SDK采用合合信息经过行业验证的世界领先的OCR扫描技术，并提供：

- 集成摄像头管理界面；
- 分层APi，允许从简单集成到复杂的UI自定义的所有内容；
- 轻巧，无需接入互联网
- 企业级安全标准
- 采用OCR技术解析数据

IDCard是[合合信息](http://www.ccint.com)开发的SDK系列的一部分。

# 目录

* [要求](#user-content-要求)
* [快速开始](#user-content-快速开始)
   * [IDCard SDK 入门](#user-content-快速开始)
      * [初始集成配置](#初始集成配置)
      * [引入头文件](#引入头文件)
      * [开始识别并获取识别结果](#开始识别并获取识别结果)
   * [IDCard SDK 高级](#user-content-高级)
* [版本记录](#user-content-版本记录)


# <a name="要求"></a>要求
SDK是IntSig OCR SDK框架演示集成示例应用程序，该SDK可以部署在iOS8.0或更高的版本；

如使用相机带有自动对焦功能时，SDK的性能会有明显提高。因此，SDK在iPad mini、2和iPod Touch4代设备上性能相对降低，因为这些设备[没有自动对焦功能](http://www.adweek.com/socialtimes/ipad-2-rear-camera-has-tap-for-auto-exposure-not-auto-focus/12536)

# <a name="快速开始"></a>快速开始

## IDCard SDK 入门

快速入门指南将帮组您尽快完成OCR识别。集成过程中需要您按照指南中描述的所有步骤进行操作。

指南中同时设置基本的原始OCR解析，以及IDCard OCR示例应用程序。在集成之前，我们建议您先尝试运行示例应用。示例应用程序可以正常的在您iOS设备或模拟器中编译和运行。

示例应用程序的源代码可以在集成期间用作参考使用。

### 初始集成配置

[下载](http://www.ccint.com)最新版本（下载后的.zip减压完成，分别由"[Demo：示例应用程序]()、[Documentations：集成文档]()、[Release：静态库]()"组成）


- 将`Relase`文件夹下的`ISIDReaderPreviewSDK.framework`和`ISOpenSDKFoundation.embeddedframework`添加到您的工程项目中

- 在Xcode项目中，打开Project导航器，将`ISIDReaderPreviewSDK.framework`和`ISOpenSDKFoundation.embeddedframework`一起拖动项目中，最好放入到Frameworks中。询问时，请你选择"Create groups"和"Copy items if needed"

![tips1](https://ccidownload.blob.core.chinacloudapi.cn/download/2019/gitbook/img/ios_tips/tips_1.jpeg)


- 将其他部分框架和系统库添加到项目中。（TARGETS -> Build Phases）

   - AudioToolbox.framework

   - libc++.tbd

   - AVFoundation.framework

![tips2](https://ccidownload.blob.core.chinacloudapi.cn/download/2019/gitbook/img/ios_tips/tips_2.jpeg)


- 在您的工程项目中配置”TARGETS -> Build Settings -> Other Linker Flags = `-ObjC`”

![tips3](https://ccidownload.blob.core.chinacloudapi.cn/download/2019/gitbook/img/ios_tips/tips_3.jpeg)


- iOS10以后相机权限信息:在`Info.plist`文件中添加如下配置:

   - NSCameraUsageDescription

   - NSPhotoLibraryUsageDescription

![tips4](https://ccidownload.blob.core.chinacloudapi.cn/download/2019/gitbook/img/ios_tips/tips_4.jpeg)

### 引入头文件

在要使用识别模块中使用import导入头文件

```objc
#import <ISIDReaderPreviewSDK/ISIDReaderPreviewSDK.h>
#import <ISOpenSDKFoundation/ISOpenSDKFoundation.h>
```

### 开始识别并获取识别结果

在启动OCR识别过程，请确保您已经拿到由于[合合信息]()授权的appkey信息，同时请您确认在应用中添加OCR识别功能的位置。通常有一个OCR按钮，当按下按钮时，开始OCR识别。然后将初始化的代码放入该按钮触发处理程序中，在这里我们将列出触发OCR识别的初始化代码。

```objc
#import "ViewController.h"
#import <ISOpenSDKFoundation/ISOpenSDKFoundation.h>
#import <ISIDReaderPreviewSDK/ISIDReaderPreviewSDK.h>

#define IS_APPKEY @"d90be979cc79afb204902eaff1-vagfvt"
#define IS_SUBKEY @""

@interface ViewController ()<ISOpenSDKCameraViewControllerDelegate>

@end

@implementation ViewController

- (void)viewDidLoad {
[super viewDidLoad];
// Do any additional setup after loading the view, typically from a nib.
}
#pragma mark 1.1 启动OCR识别
- (IBAction)clickStartOCRMethod:(id)sender
{
    ISOpenSDKCameraViewController *openCameraVC = [[ISIDCardReaderController sharedISOpenSDKController] cameraViewControllerWithAppkey:IS_APPKEY subAppkey:IS_SUBKEY];
    openCameraVC.shouldHightlightCorners = YES; 
    openCameraVC.customInfo = @"请将身份证放入框内即可自动识别"; 
    openCameraVC.delegate = self;
    [self.navigationController pushViewController:openCameraVC animated:YES];
}

#pragma mark 1.2 获取OCR结果（ISOpenSDKCameraViewControllerDelegate）
- (void)cameraViewController:(UIViewController *)viewController didFinishRecognizeCard:(NSDictionary *)resultInfo cardSDKType:(ISOpenPreviewSDKType)sdkType
{
    if (resultInfo.count > 1)
    {
        NSLog(@"识别成功");
        NSDictionary *cardItemInfo = resultInfo[kOpenSDKCardResultTypeCardItemInfo];
        NSString *name = cardItemInfo[kCardItemName];
        NSString *gender = cardItemInfo[kCardItemGender];
        NSString *nation = cardItemInfo[kCardItemNation];
        NSString *birthday = cardItemInfo[kCardItemBirthday];
        NSString *address = cardItemInfo[kCardItemAddress];
        NSString *IDNumber = cardItemInfo[kCardItemIDNumber];
        NSString *authority = cardItemInfo[kCardItemIssueAuthority];
        NSString *validity = cardItemInfo[kCardItemValidity];
    }else
    {
        NSLog(@"当前证件无法识别");
    }

}

@end
```


# <a name="版本记录"></a>版本记录


![build_version](https://img.shields.io/badge/version-v3.0.0-green.svg) 


- 提供部分预览界面源代码；

- 修复部分功能性bug

![build_version](https://img.shields.io/badge/version-v2.0.0-blue.svg) 


- 移除opencv支持

- 优化少数民族身份证识别率

![build_version](https://img.shields.io/badge/version-v1.0.0-blue.svg) 

- 创建SDK Demo

- 新增集成文档说明



