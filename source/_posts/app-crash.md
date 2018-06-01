---
title: 安卓App稳定性之旅--记Crash率 <=0.1% 实践
date: 2017-06-30 17:22:54
tags: Android
categories: 技术
---

## 稳定性的意义

在移动应用性能方面，崩溃带来的影响是最为严重的。

移动应用崩溃主要是由操作系统引发，是指应用在运行过程中出现的强制关闭(Force Closing)现象，从而**打断用户正在进行的操作体验**。

应用崩溃可以造成关键业务中断、用户留存率下降、品牌口碑变差、生命周期价值下降等影响。

根据统计数据显示，当iOS的崩溃率超过0.8%，Android的崩溃率超过0.4%的时候，**活跃用户有明显下降态势**。

<!--more-->

## 行业标准

![image](http://note.youdao.com/yws/api/personal/file/WEBcc64e9363f64d985f70ca7780f050d79?method=download&shareKey=be787dc8894f561b5b893f37a58523f1)

Android行业标准：
- 优秀App：0%-0.2%
- 标准App：0.2%-0.4%

而作为一个有追求的技术团队，我们追求一个有挑战的标准：**Crash Session<=0.1%** 或者说**Crash Free Session>=99.9%**

：）

## 改进前

![image](http://note.youdao.com/yws/api/personal/file/WEBf1cddb3b83dc5bf4572e26b0a1d5af42?method=download&shareKey=6bf0a9a43857f91805c4a6d278c8a773)

分析：
- Crash平均在0.3%
- 偶尔触达0.2%
- 某时间段一度高于0.8%（不稳定）
- 从未达到过0.1%

评价：处于行业**标准水平**，偶尔有隐患版本发布

## 三板斧
1. 磨刀不误砍柴工：**改进Crash上报系统**

    每个app都有Crash上报系统，手机证券采用的是百度SDK。而它不能将线上混淆后的代码映射成开发代码，因此很难定位问题。
    
    因此我们将百度SDK替换成Fabric。
    
    **百度SDK：**
    
    ![image](http://note.youdao.com/yws/api/personal/file/WEBa60ce6119597963e51236f20fde798e8?method=download&shareKey=a99cc3ebb50c3d8d1f1eaf624d613441)

    **Fabric：**
    
    ![image](http://note.youdao.com/yws/api/personal/file/WEB58122efe362532c7cb71cf301cf5a9cc?method=download&shareKey=9af5e900d350cc416c0391014388bb4c)

2. 第一板斧：**解决Crash问题**

    现在的当务之急当然是解决已有Crash问题了。
    
    ![image](http://note.youdao.com/yws/api/personal/file/WEBfa9c50014addfa509e1f0dcf0eb39c09?method=download&shareKey=6fa779722ab01350532c1c69f7a7c813)
    
3. 第二板斧：**提高编码质量**

    高质量代码是稳定性的基石，在当前背景下（较多需求开发），我们有没有工具能高效地帮助我们提高代码质量呢？，能有立竿见影效果呢？
    
    静态扫描工具：**Lint、Findbugs**
    
    - Lint：安卓自带的代码扫描工具
    
        通过它对Android工程源代码进行扫描和检查，可发现潜在的问题。
        
        主要包括：xml文件中是否存在hardcode、unused resources、probable bug等等。
    
    - Findbugs是java的静态分析工具
    
        它检查类或者JAR 文件，将字节码与一组缺陷模式进行对比以发现可能的问题。
    
        Findbugs自带检测器，其中有60余种Bad practice，80余种Correctness，1种 Internationalization，12种Malicious code vulnerability，27种Multithreaded correctness，23种Performance，43种Dodgy。
    
    通过这两个工具的扫描报告，可以找到很多代码的逻辑错误、隐藏问题、性能问题等一般共性问题。
    
    同时我们也要认识到这类工具的局限性。并通过自定义配置来避免“噪音”。
    
    **lint：**
    
    ![image](http://note.youdao.com/yws/api/personal/file/WEB9c22cbfb40a568b916ea258117c0140f?method=download&shareKey=9f3a2b88fa71ceefc272115bf22ed6ae)
    
    ![image](http://note.youdao.com/yws/api/personal/file/WEB101924f1bfb7ccc13ce60ed40668a997?method=download&shareKey=8ceaddfc025d94f3435a8eab104c47d5)
    
    **findbugs：**
    
    ![image](http://note.youdao.com/yws/api/personal/file/WEBb73094bf41894994d06404c19fa85686?method=download&shareKey=2c84acec973e684643bc504f96165389)
    
    ![image](http://note.youdao.com/yws/api/personal/file/WEB3e479ac09f87d7ea64bc05f14fcd0338?method=download&shareKey=ae3c0ea97a59b8df3d952b41e29291c3)
    
4. 第三板斧：**灰度**

    测试遗漏问题就这样放出去吗？有隐藏bug怎么办？
    
    祭出王牌：**灰度发布**
    
    所谓的灰度发布，简单来讲，就是不要一开始就让所有用户下载安装应用，而是先覆盖一小部分用户！
    
    发布不是简单的从0到1，不是非黑即白，在中间有一个缓冲的**灰色地带**。
    
    通过灰度发布，真实用户的真实场景测试，我们可以更全面、更深入的收集问题，修复问题。
    随着灰度覆盖范围的增加，暴露的问题也越来越充分，而当全量发布的时候，一定是一个稳定的版本！
    
    目前的策略：先在某一个渠道灰度，当问题暴露的足够多时，再发布全量版本。

## 改进后

![image](http://note.youdao.com/yws/api/personal/file/WEB4e91928c6809c2b0cb92262d2edf8d1b?method=download&shareKey=f654fcd4535bc1d511f4cfb87f17e09b)

## Never Stop

### 目标：长期稳定在<=0.1%

### 后续规划的实践

1. 编程维度：
    
    - 持续解决收集到的Crash问题
    
    - OOM和内存泄漏问题：
        - 通过LeakCancary来检测内存泄漏问题，并解决问题。
        - 通过内存检测工具来检测内存占用情况，并优化问题。
        - 通过技术选型，寻找更好的图片管理框架。
    
    - 编码规范：编码规范的重要性我就不阐述了
        1. 统一团队内编码规范，这里可以参考：[阿里巴巴的Java开发手册](http://suo.im/43uTk5)，站在巨人的肩膀上。
        2. 生成编码规范的IDE（Android Studio）配置，工程师导入配置之后，可以非常方便的用快捷键 Reformate Code
        3. 使用静态扫描工具CheckStyle和Lint来检查代码规范。
    
    - 代码重复度：
        1. 通过静态扫描工具检测重复代码。
        2. 抽取重复代码，提供工具类及底层基础类。

    - 复杂度：
        - 框架升级：一个好的框架可以减少工程师的代码量，提高效率。
        - Kotlin：语言级的改进。简洁的语法，以及NullSafty特性都是非常好的特性。

2. 流程化及工具维度：
    - 结对编程：主要是在前期设计和疑难模块编写时使用，希望取长补短，寻求更高质量的代码。
    - CodeReview：在代码提交的流程上使用CodeReview机制。
    - 在Jenkins中集成静态扫描插件：findbug、lint、CheckStyle、PMD等。
    
3. 测试维度：
    - 充分的开发自测：自己写的代码，自己最清楚会有什么问题，开发自测发现问题的效率最高。
    - 单元测试：
        - 引入单元测试框架：junit、mockito、robolectric
        - 静态扫描工具：单元测试覆盖率
    - 兼容性测试
    - monkey测试
    
4. 发布维度：
    - 灰度2.0
    
        当前灰度策略其实还不完善，后续我们会提供一种更完善的灰度机制：根据用户的应用版本号，手机型号，UUID等信息来选择灰度的用户，通过弹对话框的方式提示用户升级。

        这样我们就能很方便的从多个维度来灰度，比如：Android7.0里面5%的用户。
    
    - 终极杀招：**热修复**
    
        通过热修复技术，客户端可以发布补丁来解决线上版本的稳定性问题，而无需发版本。
        
        热修复作为当下热门的技术，在业界内比较著名的有阿里巴巴的AndFix、Dexposed，腾讯QQ空间的超级补丁和微信的Tinker。