# 基础文档
> 文档仅支持Android, iOS由于使用swift,暂未引入B/C桥
> [基础文档 ](https://www.tapd.cn/20455391/markdown_wikis/#1120455391001007811)

# 数据通信
## 单次跳转: Native <-> Weex

> 通过路由中的参数, 实现参数互传
> 实现方案: Native与Weex都支持通过url跳转
> 使用方法: url中通过get请求支持的参数类型传递

## 实时: Native -> Weex
#### onNativeStatusUpdate
> Native -> Weex
> 通用事件: 当客户端状态发生变化 / 客户端主动给weex传输数据
> 实现方案: Native给App中所有的weex实例, 发送weex支持的`globalEvent`事件
> 使用方法: 在weex页面使用``globalEvent``监听事件并获取参数

- Native发送参数: 
  | 字段   | 类型   | 默认值 | 必填 | 含义                             |
  | ------ | ------ | ------ | ---- | -------------------------------- |
  | type   | String | 无     | Y    | 与业务事件有关,用来区分事件类型  |
  | unique | String | -      | N    | 页面来源唯一标识(通常包含时间戳) |
  | id     | String | -      | -    | ID(通常为关键操作中接口返回的ID) |
  | status | Number | -      | -    | 状态位                           |
  | data   | Object | -      | -    | 数据                             |

- **Weex中`globalEvent`事件参数** : 同Native发送参数
- **已使用文档**

  | 事件                      | type           | unique                   | id                                           | status | data |
  | ------------------------- | -------------- | ------------------------ | -------------------------------------------- | ------ | ---- |
  | weex页面关闭              | `WEEX_CLOSE`   | 无                       | 当前页面路由名称(例如: `addressbook?type=2`) | 无     | 无   |
  | 修改门店资料              | `SHOP_MODIFY`  | 无                       | 门店id                                       | 无     | 无   |
  | 门店签到                  | `SHOP_CHECKIN` | 无                       | 门店id                                       | 无     | 无   |
  | 选择门店流程/创建门店流程 | `SHOP_SELECT`  | 流程前传递的参数`target` | 选择的门店 `shopId`                          | 无     |      |
  | 客户数据更新完成          | `AGENT_UPDATE` | 无                       | 无                                           | 无     | 无   |
  | 门店数据更新完成          | `SHOP_UPDATE`  | 无                       | 无                                           | 无     | 无   |
  | 企业数据更新完成          | `TEAM_UPDATE`  | 无                       | 无                                           | 无     | 无   |
  | 键盘高度弹出收起 | `KEYBOARD`  | 无 | 无 | 无 |{height:xxx}
  | 键盘将要弹出 | `KEYBOARD_WillShow`  | 无 | 无 | 无 |{width:键盘宽,height:键盘高}
  | 键盘将要收起 | `KEYBOARD_WillHide`  | 无 | 无 | 无 |{width:键盘宽,height:键盘高}
  | 存在新版本 | `VERSION_UPDATE` | 无 | 无 | 无 | 无
  | 客户对比管理 | `AGENT_COMPARE` | 无 | 无 | 无 | 无

备注：Android无相关系统API提供，KEYBOARD_WillShow和KEYBOARD_WillHide为iOS独有

## 实时: Weex -> Native
#### sendToNativeData
> weex -> Native
> 实现方案: Weex通过module传递给Native, Native(iOS使用通知)  广播数据
> 使用方法: Weex调用桥, Native通过各自实现的 模块间通讯 获取数据

- 参数
  | 字段 | 类型   | 默认值 | 必填 | 含义                            |
  | ---- | ------ | ------ | ---- | ------------------------------- |
  | type | String | 无     | Y    | 与业务事件有关,用来区分事件类型 |
  | data | Object | 无     | N    | weex想要回传给Native的数据      |

- 回调
  
  - 事件完成
- **已使用文档**
  | 事件                    | type               | data                                      | 回调     |
  | ----------------------- | ------------------ | ----------------------------------------- | -------- |
  | 更新我的客户            | `updateMyCustomer` | 无                                        | 更新完成 |
  | 更新我的门店            | `updateMyShop`     | 无                                        | 更新完成 |
  | 更新我的企业            | `updateMyTeam`     | 无                                        | 更新完成 |
  | 设置主页Tab红点待处理数 | `setBadge`         | badgeObject                               | 无       |
  | 门店签到完成或取消      | `shopCheckin`      | `String`: `SUCCSEE/CANCEL` 签到完成或取消 | 无       |
  | 选择经纪人              | `agentSelect`      | selectObject                              | 无       |

 - `selectObject`
| 字段   | 类型     | 含义       |
| ------ | -------- | ---------- |
| unique | `String` | 唯一标识   |
| agent  | `Object` | 经纪人对象 |

 - `badgeObject`
| 字段      | 类型     | 含义                  |
| --------- | -------- | --------------------- |
| tab       | `String` | Tab名称:`HOME`首页... |
| badgeText | `String` | 待处理数              |
# 其他桥

### socialShare

第三方分享
- 框架：`Weex` `Webview`
- 客户端及版本：`T2.5+`
- 功能：第三方社交分享，分享后需要将结果通过 `callback` 将 `ShareResultType` 回调
- 参数：
  - `ShareType data`
- 响应：无

```json
ShareType {
  // 分享平台枚举
  platform: Enum { "WECHAT_SESSION", "WECHAT_TIMELINE", "WECHAT_FAVORITE" , "WXWORK", "QQ", "SYSTEM_SHARE" }, 
  // 分享类型枚举
  type: Enum{ "message", "image",  "music", "video", "web" , "miniProgram" }, 
  shareContent: `ShareContent`
}

ShareResultType {
  status: Number, // 0-取消 / 1-成功，无法取得真实结果的视为 1
}
```

#### 分享内容字段描述

##### 分享文本

| 字段    | 类型   | 含义               | 备注 |
| ------- | ------ | ------------------ | ---- |
| message | String | 发送消息的文本内容 |      |

```json
shareContent = {
  message: '分享的文本内容'
}
```



##### 分享图片

| 字段     | 类型   | 含义       | 备注                   |
| -------- | ------ | ---------- | ---------------------- |
| imageUrl | String | 分享的图片 | 图片内容大小不超过10MB |

```json
shareContent = {
  imageUrl: 'http://xxxx.png'
}
```



##### 分享网页

| 字段        | 类型   | 含义          | 备注                   |
| ----------- | ------ | ------------- | ---------------------- |
| webpageUrl  | String | 网页url(必填) | 限制长度不超过10KB     |
| title       | String | 消息标题      | 限制长度不超过512Bytes |
| description | String | 描述内容      | 限制长度不超过1KB      |
| imageUrl    | String | 缩略图        | 限制内容大小不超过32KB |

```json
shareContent = {
  webpageUrl = 'https://www.baidu.com',
  title: '标题',
  description: '描述',
  imageUrl: 'http://xxx.png' // 缩略图
}
```



##### 分享小程序

| 字段        | 类型   | 含义                          | 备注               |
| ----------- | ------ | ----------------------------- | ------------------ |
| userName    | String | 小程序的原始id/userName(必填) |                    |
| webpageUrl  | String | 兼容低版本的网页链接          | 限制长度不超过10KB |
| path        | String | 小程序的页面路径              |                    |
| title       | String | 小程序标题                    |                    |
| description | String | 小程序描述                    |                    |

```json
shareContent = {
  userName: '111',
  webpageUrl: 'https://www.baidu.com',
  path: './home',
  title: '小程序标题',
  description: '小程序描述',
}
```



##### 分享音乐

| 字段                | 类型   | 含义                                | 备注               |
| ------------------- | ------ | ----------------------------------- | ------------------ |
| musicUrl            | String | 音频网页的URL地址（必填）           | 限制长度不超过10KB |
| musicLowBandUrl     | String | 供低带宽环境下使用的音频网页URL地址 |                    |
| musicDataUrl        | String | 音频数据的URL地址                   |                    |
| musicLowBandDataUrl | String | 供低带宽环境下使用的音频数据URL地址 |                    |

```json
shareContent = {
  musicUrl: 'https://music.xxx.mp3',
  musicLowBandUrl: '',
  musicDataUrl: '',
  musicLowBandDataUrl: '',
}
```



##### 分享视频

| 字段            | 类型   | 含义                           | 备注               |
| --------------- | ------ | ------------------------------ | ------------------ |
| videoUrl        | String | 视频链接(必填)                 | 限制长度不超过10KB |
| videoLowBandUrl | String | 供低带宽的环境下使用的视频链接 | 限制长度不超过10KB |

```json
shareContent = {
  videoUrl: 'https://video.xxx.mp4',
  videoLowBandUrl: ''
}
```



参考: [android](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317340&token=&lang=zh_CN)
参考: [iOS](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317332&token=&lang=zh_CN)

## getAddressBookData

> 获取通讯录数据

- 参数:  无
- 回调

  | 类型                       | 默认值 | 必填 | 含义       |
  | -------------------------- | ------ | ---- | ---------- |
  | `Department`(详见接口文档) | 无     | Y    | 通讯录数据 |

------



##  getNativeRealmData

> 通用事件:  weex从Native的Realm数据库中获取数据

- 参数

  | 字段    | 类型   | 默认值 | 必填 | 含义                                                         |
  | ------- | ------ | ------ | ---- | ------------------------------------------------------------ |
  | type    | String | 无     | Y    | <a href="getNativeRealmDatatype">区分获取类型,详见`type`字段说明文档</a> |
  | count   | Number | 无     | Y    | 获取个数                                                     |
  | next_id | String | ""     | Y    | 分页数据,**第一页传空字符串**                                |
  | filter  | Object | 无     | N    | 筛选规则, 详见文档(TODO)                                     |

  - <a name="getNativeRealmDatatype">`type`字段说明文档</a>

    | 值        | 含义     |
    | --------- | -------- |
    | customers | 我的客户 |
    | shops     | 我的门店 |
    | teams     | 我的企业 |
  
- 回调

  | 字段    | 类型     | 默认值 | 必填 | 含义                             |
  | ------- | -------- | ------ | ---- | -------------------------------- |
  | data    | [Object] | 无     | Y    | Object为realm数据字段的key,value |
  | next_id | String   | 无     | Y    | 下一页分页                       |
  

  

## setActionStatus

> 设置右上角按钮状态

- 同基础文档中的方法, 只扩展了参数	

  - disable: [Bool], 按钮禁用状态, 默认false

  

## getLoginUserInfo

> 获取登录用户信息

- 参数: 无

- 回调

  | 类型                   | 默认值 | 必填 | 含义       |
  | ---------------------- | ------ | ---- | ---------- |
  | `Member`(详见接口文档) | 无     | Y    | 通讯录数据 |

## logout

> 退出登录，跳转登录页（token失效)

- 参数: 无 

## getEnv

> 获取Native环境

- 参数: 无

- 回调:

  | 字段            | 类型   | 必填 | 含义                                       |
  | --------------- | ------ | ---- | ------------------------------------------ |
  | env             | String | Y    | `dev`代表开发环境,  值为`prod`代表生成环境 |
  | statusBarHeight | Number | Y    | **仅Android** 状态栏高度                   |
  | actionBarHeight | Number | Y    | **仅Android** 导航栏高度                   |





## wechatShare

> 微信分享



| 字段     | 类型     | 必填 | 默认        | 含义 | 可选值                                                |
| -------- | -------- | ---- | ----------- | ---- | ----------------------------------------------------- |
| `scence` | `String` | Y    | `Session`   | 场景 | `Session`/`Timeline`/`Favorite`                       |
| `type`   | `String` | Y    | ``message`` | 类型 | `message`/`image`/`music`/`video`/`web`/`miniProgram` |
| `obj`    | `Obj`    | Y    | `{}`        | 值   | 见`obj`字段说明                                       |



`obj`通用字段

| 字段          | 类型     | 必须 | 默认 | 含义        |
| ------------- | -------- | ---- | ---- | ----------- |
| `title`       | `String` | N    | 无   | 消息标题    |
| `description` | -        | N    | -    | 描述内容    |
| `thumbUrl`    | -        | N    | -    | 缩略图的url |



各`type`对应`obj`属性

| 类型`type`    | `obj`字段             | 类型     | 必须 | 默认 | 含义                                                         |
| ------------- | --------------------- | -------- | ---- | ---- | ------------------------------------------------------------ |
| `message`     |                       |          |      |      |                                                              |
|               | `text`                | `String` | Y    | 无   | 分享内容                                                     |
| `image`       |                       |          |      |      |                                                              |
|               | `dataUrl`             | `String` | Y    |      | 图片下载链接                                                 |
|               | `pathUrl`             | `String` | Y    |      | **仅支持android**,图片的本地路径                             |
| `music`       |                       |          |      |      |                                                              |
|               | `musicUrl`            | `String` |      | -    | 音频网页的URL地址                                            |
|               | `musicLowBandUrl`     | `String` |      | -    | 供低带宽环境下使用的音频网页URL地址                          |
|               | `musicDataUrl`        | `String` |      | -    | 音频数据的URL地址                                            |
|               | `musicLowBandDataUrl` | `String` |      | -    | 供低带宽环境下使用的音频数据URL地址                          |
| ``video``     |                       |          |      |      |                                                              |
|               | `videoUrl`            | `String` |      | -    | 视频链接                                                     |
|               | `videoLowBandUrl`     | -        |      | -    | 供低带宽的环境下使用的视频链接                               |
| `web`         |                       |          |      |      |                                                              |
|               | `webpageUrl`          | `String` | Y    | -    | html链接                                                     |
| `miniProgram` |                       |          |      |      |                                                              |
|               | `webpageUrl`          | `String` |      |      | 兼容低版本的网页链接                                         |
|               | `userName`            | `String` |      |      | 小程序的原始id/userName                                      |
|               | `path`                | `String` |      |      | 小程序的页面路径                                             |
|               | `hdImageDataUrl`      | `String` |      |      | **仅支持iOS**,小程序新版本的预览图二进制数据，6.5.9及以上版本微信客户端支持 |
|               | `miniprogramType`     | `String` |      |      | 可选值: 正式: WXMiniProgramTypeRelease, 测试:WXMiniProgramTypeTest, 体验:WXMiniProgramTypePreview |
|               | `withShareTicket`     | `Boolen` |      |      | 是否使用带shareTicket的分享                                  |

------

## openImagePicker

> 打开图片选择器, 选择后上传图片, 回调上传的地址

##### 参数

| 字段                 | 类型   | 默认值 | 必填 | 含义                                      |
| -------------------- | ------ | ------ | ---- | ----------------------------------------- |
| canTakePhoto         | Boolen | true   | N    | 是否可以拍摄照片                          |
| maxCount             | Number | 3      | Y    | 最大可选图片张数                          |
| minCount             | Number | 无     | N    | 最小可选图片张数                          |
| canPickOriginalPhoto | Boolen | true   | N    | **仅iOS**,是否可以选择原图                |
| canCrop              | Boolen | false  | N    | **仅iOS**,`maxCount`=1时,是否可以裁剪图片 |

##### 回调

| 类型                     | 默认值 | 必填 | 含义                         |
| ------------------------ | ------ | ---- | ---------------------------- |
| [`ImagePickerResObject`] | 无     | Y    | 见`ImagePickerResObject`文档 |

- `ImagePickerResObject`

  | 字段   | 类型   | 含义                 |
  | ------ | ------ | -------------------- |
  | url    | String | 图片上传后的url      |
  | status | Number | `1`成功 / `0`失败    |
  | error  | Object | 包含`code`,`message` |

  
## saveImage

> 保存图片
##### 参数
| 字段 | 类型   | 默认值 | 必填 | 含义         |
| ---- | ------ | ------ | ---- | ------------ |
| url  | String | 无     | Y    | 图片下载链接 |
##### 回调
- 无回调
- 图片的下载失败和保存失败由Native处理, Native会有相应的弹框提示

## saveImages

> 保存图片
##### 参数
| 字段 | 类型  | 默认值 | 必填 | 含义             |
| ---- | ----- | ------ | ---- | ---------------- |
| urls | Array | 无     | Y    | 图片下载链接数组 |
##### 回调
- [`SaveImageResult`]
| 字段     | 类型    | 默认值 | 必填 | 含义             |
| -------- | ------- | ------ | ---- | ---------------- |
| ok       | Boolean | 无     | Y    | 图片保存是否成功 |
| url      | String  | 无     | Y    | 图片url          |
| errorMsg | String  | 无     | N    | 图片保存失败原因 |
## copyToClipboard
> 复制到剪贴板
##### 参数
| 字段    | 类型 | 默认值 | 必填 | 含义       |
| ------- | ---- | ------ | ---- | ---------- |
| content | 内容 | 无     | Y    | 复制的内容 |

##### 回调
- 无

## openWheelPicker
> 打开滚动选择器
##### 参数
| 字段     | 类型   | 默认值 | 必填 | 含义                                 |
| -------- | ------ | ------ | ---- | ------------------------------------ |
| type     | String | 无     | Y    | 选择器类型                           |
| selected | Array  | 无     | N    | 已选择项：['北京市','北京','东城区'] |
| extra    | Json   | 无     | N    | 扩展字段                             |

##### extra参数
| 字段            | 类型    | 默认值 | 必填 | 含义                                           |
| --------------- | ------- | ------ | ---- | ---------------------------------------------- |
| supportCountry  | Boolean | 无     | N    | 支持全国，默认为false                          |
| supportProvince | Boolean | 无     | N    | 支持全省，默认为false                          |
| supportCity     | Boolean | 无     | N    | 支持全市，默认为false                          |
| level           |         | 无     | N    | PROVINCE、CITY、DISCRICT、AREA，默认为DISCRICT |

##### type类型
| type            | 含义       |
| --------------- | ---------- |
| `CITY`          | 城市行政区 |
| `CHECKIN_TYPE`  | 签到类型   |
| `EMPLOYER_TYPE` | 店长评价   |
| `EMPLOYEE_TYPE` | 店员态度   |
##### 回调
| 字段             | 类型  | 默认值 | 必填 | 含义     |
| ---------------- | ----- | ------ | ---- | -------- |
| [`SelectResult`] | Array | 无     | Y    | 选择结果 |

- `SelectResult`
  | 字段 | 类型   | 含义 |
  | ---- | ------ | ---- |
  | key  | String | key  |
  | name | String | name |

## hideSoftInput
> 隐藏软键盘 

- 参数 无

## showPhotoBrowser
> 图片浏览
##### 参数
| 字段   | 类型    | 默认值 | 必填 | 含义                             |
| ------ | ------- | ------ | ---- | -------------------------------- |
| title  | String  | 无     | N    | 图片浏览器标题                   |
| index  | Number  | 无     | N    | 点击查看的图片在当前图片集的位置 |
| images | Array   | 无     | Y    | 图片对象数组                     |
| save   | Boolean | true   | N    | 是否可保存本地                   |
| extra  | Object  | 无     | N    | 扩展字段（暂时用不到）           |

- `images`
  | 字段 | 类型   | 含义     |
  | ---- | ------ | -------- |
  | url  | String | 图片地址 |

## openAmap
> 打开高德地图，若无安装则跳转网页
##### 参数
| 字段    | 类型   | 默认值 | 必填 | 含义           |
| ------- | ------ | ------ | ---- | -------------- |
| lat     | Number | 无     | N    | 纬度           |
| lon     | Number | 无     | N    | 经度           |
| poiName | String | 无     | N    | 名称           |
| type    | Number | 无     | N    | 0-标注，1-导航 |

## getRealmModelsCount
> 获取指定对象集列表数据总数
##### 参数
| 字段   | 类型     | 默认值 | 必填 | 含义               |
| ------ | -------- | ------ | ---- | ------------------ |
| models | [String] | 无     | N    | 需要查询的对象模型 |
##### models类型
| 类型  | 含义 |
| ----- | ---- |
| agent | 客户 |
| shop  | 门店 |
| team  | 企业 |

##### 回调
| 类型     | 默认值 | 必填 | 含义       |
| -------- | ------ | ---- | ---------- |
| [Number] | 无     | Y    | 查询统计数 |

## getClientConfig 
> 获取客户端全局配置
> v1.1.0
##### 参数
| 字段 | 类型     | 默认值 | 必填 | 含义                                          |
| ---- | -------- | ------ | ---- | --------------------------------------------- |
| type | String   | 无     | Y    | 类型                                          |
| keys | Number[] | 无     | N    | 批量查询配置的key数组，如['440300', '440351'] |

- `type`
  | 字段                  | 类型   | 含义                                           |
  | --------------------- | ------ | ---------------------------------------------- |
  | CHECKIN               | String | 签到类型（T1.13 使用 `config.newCheckinType`） |
  | CHECKIN_EMPLOYER      | String | 店长评价（T1.13 废弃）                         |
  | CHECKIN_EMPLOYEE      | String | 店员评价（T1.13 废弃）                         |
  | OPEN_CITIES           | String | 开通城市                                       |
  | TEAM_VIP_TIME         | String | 企业搜索-会员情况-vip时间                      |
  | TEAM_MEMBER_COUNT     | String | 企业搜索-会员情况-成员数目                     |
  | AGENT_JOB_TITLE       | String | 客户职称                                       |
  | ALL_CITY              | String | 全国城市配置（eg:省、市·、行政区、片区）       |
  | TICKET_DATA           | String | 系统工单标题                                   |
  | REPORT_TYPE           | String | 报表类型                                       |
  | TEAMWEEKLYACTIVE_TYPE | String | 企业搜索-周活情况                              |
  | TRAININGSCORE_TYPE    | String | 培训-反馈平均分                                |
  | AFTER_SALE_GRADE      | String | 售后评分                                       |
  | MOMENT_TYPE           | String | 员工动态                                       |
  | CONTRIBUTE_TYPE       | String | 贡献标签                                       |
  | SERVICE_SYSTEM_TYPE   | String | 线索客户-使用系统                              |
  | REFERRAL_TYPE         | String | 线索客户-客户来源                              |

##### 回调
| 类型     | 默认值 | 必填 | 含义                                                         |
| -------- | ------ | ---- | ------------------------------------------------------------ |
| [Object] | 无     | Y    | 没有keys返回对应type全部配置数据；有keys返回对应type中对应keys的配置项数据 |
```
有keys情况下
如regionCodes: [String, String], // ["省,市,行政区,片区", "省,市,行政区,片区"]
按照格式返回：
 [
  	[object,object,object,object],
	[object,object,object,object],
]
```


## saveLocationImage
> 一屏幕范围内，保存指定区域的图片（不支持list，scroller等滚动屏幕下的区域）
##### 参数
| 字段   | 类型   | 默认值 | 必填 | 含义                 |
| ------ | ------ | ------ | ---- | -------------------- |
| left   | Number | 无     | Y    | div左侧距离x轴偏移量 |
| right  | Number | 无     | N    | div右侧距离x轴偏移量 |
| top    | Number | 无     | Y    | div上侧距离y轴偏移量 |
| bottom | Number | 无     | N    | div上侧距离y轴偏移量 |
| height | Number | 无     | Y    | 高度                 |
| width  | Number | 无     | Y    | 宽度                 |

```
其他:
Weex中的使用方式，float scale = (float)originWidth / 750; originWidth为原生中的实际屏幕宽度，750为Weex在双端中的默认屏幕宽度，得到一个比例值。传输的参数 = 获取的参数 * scale。

```

## sendChatMessage
> 发送消息，原生调用腾讯云发送消息，支持文本或图片
##### 参数
| 字段        | 类型     | 默认值 | 必填 | 含义         |
| ----------- | -------- | ------ | ---- | ------------ |
| chatUserIds | [String] | 无     | Y    | 消息接收者id |
| chatData    | Object   | 无     | N    | 消息内容     |

- `chatData `
  | 字段    | 类型     | 含义     |
  | ------- | -------- | -------- |
  | content | String   | 消息内容 |
  | images  | [String] | 消息图片 |

## setNativeStorage
> 保存缓存数据到本地
##### 参数
| 字段 | 类型   | 默认值 | 必填 | 含义     |
| ---- | ------ | ------ | ---- | -------- |
| key  | String | 无     | Y    | 类别     |
| data | String | 无     | Y    | 保存对象 |

## getNativeStorage
> 获取原生缓存数据
##### 参数
| 字段 | 类型   | 默认值 | 必填 | 含义 |
| ---- | ------ | ------ | ---- | ---- |
| key  | String | 无     | Y    | 类别 |
取不到返回null

## getCurrentLocation
> 获取附近一个建筑的定位信息
##### 返回参数
| 字段       | 类型    | 默认值               | 说明            |
| ---------- | ------- | -------------------- | --------------- |
| status     | Boolean | 无                   | 是否定位成功    |
| coordinate | Arry    | [latitude,longitude] | 坐标点          |
| amapId     | String  | ''                   | 高德地图定位uid |
| name       | String  | ''                   | 定位建筑名称    |
| address    | String  | ''                   | 定位建筑地址    |

## getWebHost
> 获取H5访问地址host,由于T端H5一般都是B端页面

- 回调
	host：`String`

## getShotView
> 获取当前页面截图内容

##### 参数
| 字段         | 类型   | 默认值 | 必填 | 含义                               |
| ------------ | ------ | ------ | ---- | ---------------------------------- |
| topHeight    | Number | 无     | N    | 顶部不截取高度(ios:wx  android:px) |
| bottomHeight | Number | 无     | N    | 底部不截取高度(ios:wx  android:px) |

## showAlertView
- 功能：唤起带标题的弹窗提示，当按钮被点击后，派发全局事件 `onAlertViewItemClicked(AlertViewItemType)`
- 参数：
  - `AlertViewDataInfo data`
- 响应：无

```
AlertViewDataInfo {
  title: String, // 标题（必填）
  content: String, // 内容
  items: AlertViewItemType[],
}

AlertViewItemType {
  text: String, // 按钮的文字（必填）
  name: String, // 按钮的名称（必填，作用是便于 Weex 知道具体是哪个按钮被点了）
}
```