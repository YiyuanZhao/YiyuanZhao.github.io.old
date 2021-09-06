---
layout: project
title:  基于Selenium的安卓App自动化测试
code_url: "https://github.com/YiyuanZhao/ProfileProjectSourceCode/tree/main/seleniumAutomation"
---

# 背景
人工赚取APP积分是一件具有高度重复性的工作，可以通过自动化测试的方式减小人工的工作量。

本项目以``cn.xuexi.android``APP为例，编写了自动化测试的脚本。  
Notes: 与自动驾驶相似，尽管脚本具有全自动化的功能特性，但不能代替人工操作。

# 解决方案
自动化测试采用Appnium的Python接口进行，一些技术的细节列表如下：
 - 元素捕捉：find_element_by_id()，find_element_by_xpath()方法。
 - 回调函数触发：捕捉元素后，调用click()方法。
 - 画面滚动：TouchAction中的press()，move_to()，release()方法。

主要遵循以下的流程：
 - 自动登录
 - 取消自动更新
 - 应用权限配置
 - 新闻阅读
 - 公众号阅读
 - 视频观看
 - 统计积分并写入log

脚本运行时常为约15分钟，每日获得积分约20分。

# 源代码
你可以[在这里](https://github.com/YiyuanZhao/ProfileProjectSourceCode/tree/main/seleniumAutomation)下载项目的源代码。

# 环境需求
Android Debug Bridge version 1.0.41  
Android_Studio 模拟器(Pixel 2 API 30, Android 11.0(x86), API 30)  
Appium v1.20.2  
cn.xuexi.android.apk version 2.24.0  


* * *

# 技术栈
Python，Selenium，Appium

### [back](/)