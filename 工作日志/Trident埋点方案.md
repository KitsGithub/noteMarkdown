监控系统埋点方案(初稿)
 Trident 埋点方案

 Weex埋点统计方案
● 通过向WXSDKEngine注册相关handler以实现对应通知指标的统计
 统计方式

1.  在初始化weex环境时，调用WXSDKEngine的registerHandler:withProtocol:注册WXApmGeneratorProtocol统计协议
2.  在处理类中监听onStage:withValue:来获取具体的统计指标
3. 下文中，关于Weex的统计指标均使用对应的key值作为具体的描述
weex自带的统计指标
可参考WXApmForInstance.m这个类
1. 资源加载时间（开始、结束时间）
2. 资源从远端加载时间（开始、结束时间）
3. 首屏时间（可同步统计首屏调用js时间、调用js次数，首屏组件数量...）
4. 可交互时间（经测试发现这个时间不准）
5. 当前页指标（当前页所有组件数量、list数量、加载组件总耗时、当前页资源大小...）
...忽略100多个指标
weex扩展统计指标
在通过相关统计场景调用apmInstance实例中的setStatistic:withValue记录相关关键时间节点，作为统计指标
如：可以统计weex的网络请求耗时（Trident是通过扩展WXStreamModule来复写原来的网络请求，因此可以做统一的网络请求统计）

 参考
[可交互时间的探索和首屏时间的改进之路]
●  参考文章中原API已废弃，使用WXApmForInstance作为统治指标

 IOS埋点统计方案
待补充

埋点上报方案
待补充

100 端基础指标
非补充描述，100均为打开应用时自动上报，data参数均需要带上「环境变量」
1. 用户登录时上报安装应用
2. 破解系统权限，越狱或ROOT时上报 APP启动后上报
3. APP切入(前台运行)  统一为app启动指标（热启动）
4. APP切出(后台运行) 统一为app退出指标
5. APP启动
● 冷启动
● 热启动
6. APP退出
上报场景
1. 用户主动退出
2. app进入后台（需要记录切出后台时展示的页面）

7. 上报经纬度
8. testflight应用探测，itms-beta (安装时上报)
9. testflight应用探测，itms-beta (未安装时上报)
10. 环境参数（合并为同一个信息内容包含如下）
{
 deviceInfo: 设备信息(环境信息) 
 deviceSystem: 操作系统(环境信息) 
 deviceResolution: 分辨率(环境信息)
}


 101 端异常指标
1. 日志分析
   - 崩溃时的log收集

 102 端性能指标
1. 进入页面
对weex页面，监听wxRecordStart作为页面开始加载时间
对原生页面，以调用viewDidLoad方法作为进入时间

2. 离开页面
通用使用viewWillDisappear作为离开时间

3. 渲染时间（首屏时间）
计算方式 time = （结束时间 - 进入时间）
对原生页面，以调用viewDidAppear方法作为结束时间(待定)
对weex页面，以监听`wxNewFsRender`作为首屏时间


4. 可交互时间（1.0版本只统计weex页面）
对weex页面，以监听wxFirstInteractionView作为首屏时间
原生方案待补充


5. 页面完全加载时间（统计首屏时间 + 接口请求时间）
对weex页面，以监听wxInteraction最后一次调用时间作为完全加载时间
原生方案待定


6. 页面卸载时间（dealloc）
原生页面，以调用dealloc方法作为销毁时间

7. 新增 - 启动时间（APP启动时间）







 103 端API指标
参考：fishhook
1. 请求成功
2. 请求未发送
3. 请求异常
4. 请求超时
5. DNS解析耗时
6. TCP连接耗时
7. SSL安全连接耗时
8. 网络请求耗时
9. 数据传输耗时
