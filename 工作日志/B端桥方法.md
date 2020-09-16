## 约定

### User Agent
> 这里尖毛草不用Trident而是用Loki（`T 2.6+`），原因是Trident作为IE内核名会被Vue识别为IE环境，导致页面出错。

Weex 和 WebView 都要设置 User Agent，在默认 UA 结尾加上 `NetType/WIFI WeexResource/23 BuildType/debug Shikamaru/1.6.0`，其中：
- `NetType/WIFI` 对应当前网络类型，取值包含 `WIFI` `4G` `3G` `2G`
- `WeexResource/23` 对应当前 Weex 资源包版本（生效版本：`C4.9+` `B6.9+` `T0.11+`）
- `BuildType/DEBUG` 对应当前环境。开发环境为 `DEBUG`，测试环境、预发布环境、生产环境为 `RELEASE`（生效版本：`C4.9+` `B6.9+` `T1.3+`）
- `Shikamaru/1.6.0` 对应当前客户端及版本号。小鹿选房为 `Shikamaru`，看房日记为 `FangGeekAgent`，尖毛草为 `Loki`。`1.6.0` 对应当前版本号。为提高灰度系统识别性能，版本号必须置后

### 统一接口 callNative

- 框架：`Weex` `Webview`
- 客户端及版本：`C1.7+` `B4.0+` `T1.0+`
- 功能：实现 Weex 和 WebView 的统一异步接口，响应方式均为执行回调函数（看房日记暂不实现 WebView 中的统一异步接口）
- 参数：
  - `String name` 接口名称
  - `Object data` 调用数据
  - `Callback callback` 根据各库的实际情况而定
- 响应：
  - `String response` Response 的 JSON 字符串，可以认为所有接口都需要响应，只是业务方不需要回调而已

```
Response {
  data: Any, // 需要注意的是，对于第三方接口（如支付、分享），只要能够传参给第三方，都视作成功，并把第三方的任何回应放（不管成功失败）到 data 中
  error: {
    code: String, // 用大写下划线字符串表示错误，如 INVALID_ARGUMENTS
    extra: Any, // 任意类型的数据、信息，能给尽量给
  },
}

SuccessReponseExample {
  data: {},
  // error: null, // 该字段不存在或为空
}

ErrorResponseExample {
  // data: null, // 该字段不存在或为空
  error: {},
}
```

### Weex 或 WebView 独有接口
- Weex 按照原有 module 形式实现
- WebView 按照原有 callHandler 形式实现

### 接口通用错误
- `INVALID_INTERFACE` 接口不存在（包含两种接口，一种是和 callNative 平级的，另一种是 callNative 内的）
- `INVALID_ARGUMENTS` 参数不正确

## 统一接口（JS 调用客户端）

### getDeviceInfo

- 获取设备基础信息

返回参数结构：

| 参数            | 说明             | 类型   | 默认值 | 版本 |
| --------------- | ---------------- | ------ | ------ | ---- |
| statusBarHeight | 状态栏高度       | Number | 0      | 7.4+ |
| version         | 获取客户端版本号 | String | ""     | 7.4+ |

### canOpen

- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：检查当前设备的应用是否存在
- 参数：
  - `String name` 对于 iOS 这是 URL Schema（如 `weixin://`），对于 Android 这是包名（如 `com.tencent.mm`）
- 响应：`CheckResultType`

```
CheckResultType {
  status: Number, // 0-不存在 / 1-存在
}
```

### getToken
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：返回用户 token
- 参数：无
- 响应：`String`

### getLocation
- 框架：`Weex`
- 客户端及版本: `T1.0+`
- 功能：获取经纬度信息，用于网络请求 `Headers['X-Location']`
- 参数：无
- 响应：`String`


### openUrl
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：打开对应 URL，可能是内部协议（如 `shikamaru://`），也可能是外部协议（如 `wechat://`  `tel:10086`）
支持短链接URL，如果没有携带有Scheme的情况下，则会自动添加平台的Host进行跳转（短链接需要以/开头）
例如：`/app/discovery/contribute?url=XXX`
- 参数：
  - `String url`
- 响应：无

### goBack
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：返回上一个页面
- 参数：无
- 响应：无

### showLoading

- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：显示阻断全屏的 Loading，对应还有 `hideLoading`
- 参数：
  - `LoadingType data`
- 响应：无

```
LoadingType {
  text: String,
}
```

### hideLoading
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：隐藏阻断全屏的 Loading，对应还有 `showLoading`
- 参数：无
- 响应：无

### setLogType
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.2+` `B4.2+` `T1.0+`
- 功能：设置页面 LogType（并且自动上报进入事件 subLogType 1003，当页面销毁时还将上报离开事件 subLogType 1004）
- 参数：
  - `Number logType`
- 响应：无

### logEvent
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：上报事件给日志收集系统
- 参数：
  - `Number logType`
  - `Number subLogType`
  - `Object data`
 - `tid String`    V5.0.0添加
	- `rid1 String`    V5.0.0添加
	- `rid2 String`    V5.0.0添加
	- `rid3 String`    V5.0.0添加
- 响应：无

### getVersion
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：获取客户端版本号，格式 `MAJOR.MINOR.PATCH`，如 `1.8.1`
- 参数：无
- 响应：`String`

### openDatePicker

- 打开原生时间选择控件

入参数据格式

| 参数       | 说明                                                         | 类型   | 默认值 | 版本 |
| ---------- | ------------------------------------------------------------ | ------ | ------ | ---- |
| date       | 时间戳（默认为当前时间）毫秒级别                             | Number | 0      | 7.4+ |
| pickerType | 日期格式化类型（1：年月日时分，2：年月日, 3：年月日 上午/下午） | Number | 1      | 7.4+ |
| amOrPm     | 上午下午 （1:上午  2：下午 0：无上下午）                     | Number | 0      | 7.4+ |

```js
// 入参示例
query = {
  date: 1598355084505,
  pickerType: 1,
  amOrPm: 0,
}
```

出参数据格式

| 参数       | 说明                                                         | 类型   | 默认值 | 版本 |
| ---------- | ------------------------------------------------------------ | ------ | ------ | ---- |
| date       | 时间戳（默认为当前时间）                                     | Number | 0      | 7.4+ |
| pickerType | 日期格式化类型（1：年月日时分，2：年月日, 3：年月日 上午/下午） | Number | 1      | 7.4+ |
| amOrPm     | 上午下午 （1:上午  2：下午 0：无上下午）                     | Number | 0      | 7.4+ |

```js
// 出参示例
query = {
  date: 1598355084505,
  pickerType: 1,
  amOrPm: 0,
}
```



### setTitle

- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：设置页面标题
- 参数：
  - `String title`
- 响应：无


### setAction
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：设置右上角按钮，按数组顺序从左到右依次设置，当右上角被点击后，派发全局事件 `onActionClicked(ActionType)`
- 参数：
  - `ActionType[] data`
- 响应：无

```
ActionType {
  // 如果 `type=ICON` 则右上角是图标，图标地址为 `content`，可能是本地资源，也可能是网络图片
  // 如果 `type=TEXT` 则右上角为文字，内容为 `content`、文字颜色为 `color`、背景颜色为 `backgroundColor`
  type: Enum { "ICON", "TEXT" },
  name: String, // 便于 Weex 知道具体是哪个按钮被点了
  content: String,
  color: String, // 格式为 `#000000`
  backgroundColor: String, // 【1.7新增】格式为 `#000000`
}
```

### showToast
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：显示 Toast，对于 Android，如果 `duration` 大于 3 则为 `LONG`，否则为 `SHORT`
- 参数：
  - `ToastType data`
- 响应：无

```
ToastType {
  text: String,
  duration: Number, // 单位为秒，可以为空，默认一秒
}
```

### checkLoginState
- 框架： `Weex` `Webview`
- 客户端及版本： `C5.0.0+`
- 功能：检查登录状态，对于未登录时则自动弹起登录框，同时通过回调返回是否登录的状态
- 参数：`CheckParams`
```
CheckParams {
	showLogin: Number, //0-不自动弹出登录窗 1-自动弹起登录窗
}
```
- 响应：`CheckResultType`
```
CheckResultType {
	status: Number, // 0-未登录 / 1-已登录
} 
```


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
  | 事件         | type                      | data        | 回调     |
  | ------------ | ------------------------- | ----------- | -------- |
  | 客户关联访客 | `FJK_CLIENT_LINK_VISITOR` | {status: 1} | 关联成功 |


### showAlertView
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.6+` `B4.6+` `T1.0+`
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

### showVerticalActionSheet
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.0+` `B4.0+` `T1.0+` `T1.0+`
- 功能：唤起垂直 Action Sheet 浮层（不需要传入「取消」按钮），当按钮被点击后，派发全局事件 `onVerticalActionSheetItemClicked(VerticalActionSheetItemType)`
- 参数：
  - `VerticalActionSheetItemType[] data`
- 响应：无

```
VerticalActionSheetListType {
  title: String, // 列表标题（非必填）
  items: VerticalActionSheetItemType[],
}

VerticalActionSheetItemType {
  text: String, // 按钮的文字（必填）
  name: String, // 按钮的名称（必填，作用是便于 Weex 知道具体是哪个按钮被点了）
  red: Number, // 是否标红（非必填，默认为 0 不标红，为 1 时标红）
  // 注意：iOS 有且只有一个标红按钮，并自动放到最后一位
}
```

### showPhotoBrowser
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.6+` `B4.6+` `T1.0+`
- 功能：图片浏览器
- 参数：
  - `pictureInfo data`
- 响应：无

```
pictureInfo {
  picInfo: picInfo[], // 图片
  title: String, // 标题
  index: int, //当前显示图片索引
  showPicInfo: int, // 是否显示图片信息,0不显示(默认)，1显示（是否实拍、时间、地址等）
}

picInfo {
   exif: {
    SWGPhotoTiff: data, //图像信息
    SWGPhotoIfd: data,  //拍摄信息
    SWGPhotoGPS: data,  //GPS信息
  }
 url:String
}
```

### block
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.4+` `B4.4+`
- 功能：加入黑名单
- 参数：
  - `BlacklistType data`
- 响应：无，但要触发 `onNativeStatusUpdate.BLACKLIST`

```
BlacklistType {
  id: String, // 被拉黑者 ID，对 B 端是用户 ID，对 C 端是经纪人 ID
  name: String, // 被拉黑者昵称
}
```

### unblock
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.4+` `B4.4+`
- 功能：解除黑名单
- 参数：
  - `BlacklistType data`
- 响应：无，但要触发 `onNativeStatusUpdate.BLACKLIST`

```
BlacklistType {
  id: String, // 被拉黑者 ID，对 B 端是用户 ID，对 C 端是经纪人 ID
  name: String, // 被拉黑者昵称
}
```

### login
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：唤起登录界面
- 参数：无
- 响应：无

### logout
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：退出当前账号
- 参数：无
- 响应：无

### subscribe
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.7+`
- 功能：订阅主题，对应还有 `unsubscribeOrUpdate`
- 参数：
  - `String itemType`
  - `String itemId`
- 响应：无

### unsubscribeOrUpdate
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.7+`
- 功能：取消订阅主题或修改主题的订阅条件，对应还有 `subscribe`
- 参数：
  - `String itemType`
  - `String itemId`
- 响应：无

### postChat
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.2+`
- 功能：聊一下功能
- 参数：
  - `ForumChatType data`
- 响应：无，但要触发 `onNativeStatusUpdate.CHAT`

```
ForumChatType {
  type: String, // 聊一下类型
  id: String, // 聊一下 ID
}
```

### postGroupChat
- 框架：`Weex` `Webview`
- 客户端及版本：`B5.0+`
- 功能：聊一下功能
- 参数：
``` 
 id: String, // 聊一下 ID
 type: String, // 聊一下类型 B5.1+
```
- 响应：无，但要触发 `onNativeStatusUpdate.CHAT`


### postForumAnswer
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.0+` `B4.0+`
- 功能：社区发表回答
- 参数：
  - ForumQuestionType data
- 响应：无，但要触发 `onNativeStatusUpdate.FORUM_ANSWER`

```
ForumQuestionType {
  id: String, // 问题 ID
  questionTitle:String,//问题文本（仅C端） V5.0.0版本替换原title字段
  content: String, // 问题描述（仅C端）
  images: ImageInfoType, // 图片内容（仅C端）
}

ImageInfoType {
  url: String, // 图片地址
  width: int, // 图片宽度
  height: int, // 图片高度
}
```

### postForumComment
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.0+` `B4.0+`
- 功能：社区发表评论
- 参数：
  - ForumAnswerType data
- 响应：无，但要触发 `onNativeStatusUpdate.FORUM_COMMENT`

```
ForumAnswerType {
  id: String, // 回答 ID
}
```

### postForumQuestion
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.0+`
- 功能：社区发表问题
- 参数：
 - ForumQuestionType （非必填）
 - 响应：无，但要触发 `onNativeStatusUpdate.FORUM_QUESTION`

```
ForumQuestionType {
  id: String, // 问题 ID
  communityId, // 小区ID
  communityName，// 小区名
}
```
### pushDiscoveryAnswer
- 框架：`Weex` `Webview`
- 客户端及版本：`C5.0+`
- 功能：我的发布/回答跳转发现页tab回答提问
- 参数：无
- 响应：无

### share
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`、`T2.5+`
- 功能：社交分享，分享后需要将结果通过 `callback` 将 `ShareResultType` 回调
- 参数：
  - `ShareType data`
- 响应：无

```
ShareType {
  platform: Enum { "WECHAT_MESSAGE", "WECHAT_TIMELINE", "WXWORK_MESSAGE", "QQ", "SYSTEM_SHARE" },  // C3.2+ 废弃 "SKMR_MESSAGE"，改用bridge方法 shareMessage
  title: String, // 分享标题（必填）
  description: String, // 分享描述（选填）
  imageUrl: String, // 图片地址（选填）
  url: String, // 链接地址（必填）
}

ShareResultType {
  status: Number, // 0-取消 / 1-成功，无法取得真实结果的视为 1
}
```

### shareWeapp
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.7+`
- 功能：分享小程序卡片，小程序类型字段开发环境传「体验版」生产环境传「正式版」，分享后需要将结果通过 `callback` 将 `ShareResultType` 回调
- 参数：
  - `WeappShareType data`
- 响应：无

```
WeappShareType {
  appOriginalId: String, // 小程序原始 ID
  path: String, // 小程序 URL
  title: String, // 分享标题（必填）
  description: String, // 分享描述（选填）
  imageUrl: String, // 图片地址（选填）
  webUrl: String, // 低版本链接地址（必填）
}

ShareResultType {
  status: Number, // 0-取消 / 1-成功，无法取得真实结果的视为 1
}
```

### shareMessage
- 框架：`Weex` `Webview`
- 客户端及版本：`C3.2+`、`T2.5+`
- 功能：分享消息体到IM聊天，分享后需要将结果通过 `callback` 将 `ShareResultType` 回调
- 参数：
  - `MessageShareType data`
- 响应：无

```
MessageShareType {
  title: String, // 标题
  content: String, // 消息内容
  imageUrl: String, // 图片地址
  actionText: String, // 动作消息
  description: String, // 描述
  targetType: String, // 兼容旧版本
  webUrl: String, // 兼容旧版本
  skmrWebUrl: String, // 兼容旧版本
  showType: String, // 展示类型 (卡片样式) 
  contentType: String, // 内容类型
  contentId: String, // 内容id
  extInfo: String, // 附加信息
}

ShareResultType {
  status: Number, // 0-取消 / 1-成功，无法取得真实结果的视为 1
}
```

### showActionSheet
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：唤起 Action Sheet 浮层，当按钮被点击后，派发全局事件 `onActionSheetItemClicked(ActionSheetItemType)`
- 参数：
  - `ActionSheetListType[] data`
- 响应：无

```
ActionSheetListType {
  items: ActionSheetItemType[],
}

ActionSheetItemType {
  icon: String, // 图标的图片地址（必填，可能是本地资源，也可能是网络图片）
  text: String, // 图标的文字（必填）
  name: String, // 图标的名称（必填，作用是便于 Weex 知道具体是哪个按钮被点了）
}
```

### showWeappCodeShare
- 框架：`Weex` `Webview`
- 客户端及版本：`B5.0+`
- 功能：唤起 分享小程序 浮层，小程序类型字段开发环境传「体验版」生产环境传「正式版」。
- 参数：
  - `showWeappCodeShareType data`
- 响应：无

```
showWeappCodeShareType {
  appOriginalId: String, // 小程序原始 ID
  title: String, // 分享标题（必填）
  description: String, // 分享描述（选填）
  codeUrl: String, // 小程序码图片地址（必填）
  path: String, // 小程序页面路径（必填）
  webUrl: String, // 低版本链接地址（必填）
  imageUrl: String, // 图片地址（选填）
}
```

### showCommunityAgentRecommendation
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.0+`
- 功能：显示小区推荐经纪人界面
- 参数：
  - `CommunityInfo data`
- 响应：无

```
CommunityInfo {
  id: String, // 小区 ID
}
```

### showHouseOffers
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：显示房源的报价信息
- 参数：
  - `HouseType data`
- 响应：无

```
HouseType {
  id: String, // 房源 ID
  historyType: Number, // 【1.7新增】历史房源类型，0-默认非历史 / 1-历史
}
```

### startConversation
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：发起与 `chatUserId` 的会话
- 参数：
  - `ConversationType data`
- 响应：无

```
ConversationType {
  chatUserId: String,
 from:String,//C3.8新增，记录入口路径
  firstMessageTitle: String, // 【C1.7废弃】首次发起会话自动发消息的标题（必填）
  firstMessageDescription: String, // 【C1.7废弃】首次发起会话自动发消息的描述（选填）
  firstMessageImageUrl: String, // 【C1.7废弃】首次发起会话自动发消息的图片地址（选填）
  firstMessageUrl: String, // 【C1.7废弃】首次发起会话自动发消息的链接地址（必填）
  alwaysSendFirstMessage: Number, // 【C1.7废弃】值为 1 时忽略是否首次的判断（选填）
}
```

### getLocation
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.2+`
- 功能：获取 App 缓存的地理位置
- 参数：无
- 响应：`LocationResultType`

```
LocationResultType {
  latitude: Number, // 纬度，如果系统拒绝或出错则返回 0
  longitude: Number, // 纬度，如果系统拒绝或出错则返回 0
}
```

### addFollowUp
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.5+`
- 功能：客户主页写跟进
- 参数：
  - `customerId String`
- 响应：无


### customerCallPhone
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.5+`
- 功能：客户主页打电话
- 参数：
  - `phoneInfo Data`
- 响应：无

```
phoneInfo {
  phone: String, // 电话号码
  isAddFollowUp: Bool, // 是否增加跟进（0-否，1-是）
  customerId: String, // 客户Id
}
```

### deleteFollowUp
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.5+`
- 功能：客户主页删除跟进
- 参数：
  - `FollowUpInfo Data`
- 响应：无

```
FollowUpInfo {
  rid: String, // 跟进id
  customerId: String, // 客户Id
}
```

### recoveryCustomer
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.5+`
- 功能：恢复已删除的客户
- 参数：
  - `RecoveryInfo Data`
- 响应：无

```
RecoveryInfo {
  groupId: String, // (用来判断是否是副组长)
  customerId: String, // 客户Id
}
```

### customerMoreAction
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.5+`
- 功能：客户主页跟多操作
- 参数：
  - `CustomerData Data`
- 响应：无

```
CustomerData {
  customerId: String, // 客户Id
  groupId: String, // (用来判断是否是副组长)
  fromrecycle: int, // 是否是回收站入口
}
```

### customerMatchUnits
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.5+`
- 功能：客户主页自动匹配房源跳转
- 参数：
  - `CustomerData Data`
- 响应：无

```
CustomerData {
  customerId: String, // 客户Id
  groupId: String, // 小组id
}
```

### saveCustomer
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.5+`
- 功能：将访客存为客户
- 参数：
  - `linkInfo Data`
- 响应：无

```
linkInfo {
  userId: String, //客户id
  customerName: String, // 客户名字
  gender: int, // 客户性别
}
```

### sendNativeEvent
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.6+` `C4.12.0`
- 功能：发送通知给原生
- 参数：
  - `sendEventInfo Data`
- 响应：无

```
sendEventInfo {
  name: String, //名称
  param: Object, // 参数
}
```
- weex通知客户列表页刷新 name =UPDATE_CUSTOMER_LIST
- weex通知小组关注页刷新 name =UPDATE_SUBSCRIBE_LIST
- weex通知企业详情页面显示更多按钮 name =UPDATE_TEAMHOUSE_MOREACTION
- weex通知企业房源列表页刷新 
```
name=UPDATE_TEAMHOUSE_LIST,
	param:{
		id:String,//房源id
		isfalsity:String,//疑似无效标志
	}
```
- weex通知投稿页/行业动态检查剪贴板内容 name =CHECK_CLIPBOARD
- weex通知回答详情页退出 
```
name=ANSWER_DETAIL_FINISH,
param: {
	id: String, //回答id
}
```
- weex通知关注房产分析师列表推荐房产分析师更新状态
```
name=ANALYST_SUBSCRIBE_STATUS
param: {
	id: String, //房产分析师id
	status: Number, //关注状态 0 : 未关注  1：已关注
}
```
- weex通知企业新房列表数据刷新
```
name=TEAM_NEW_HOUSE_LIST
param: {
	id: String, //企业新房id
	actionType: Number, //刷新操作类型：1：移除
}
```

### showCompanyLogoPicker
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.7+`
- 功能：提供公司Logo选择界面
- 参数：无
- 响应：
`EstateCompanyInfo data`

```
EstateCompanyInfo {
  id: String,
  name: String,
  nameCompany: String,
  logo: String,
  group: String,
  seq: String,
  backgroundColor: String,
}
```

### teamHouseCallPhone
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.8+`
- 功能：企业详情打电话
- 参数：
  -`phoneInfo Data`
- 响应：无

```
phoneInfo {
  houseId: String,
  contactId: String,
  phone: String,
  isMLS: Number, // 1:MLS房源
}
```

### teamHouseAddFollowUp
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.8+`
- 功能：企业详情增加跟进
- 参数：
  -`houseId String`
  -`isMLS: Number`, // 1:MLS房源
- 响应：无


### teamHouseDeleteFollowUp
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.8+`
- 功能：企业详情删除跟进
- 参数：
  -`houseId String`
  -`rid String` 
  -`isMLS Number`,1: MLS房源 
- 响应：无

### editGuideVisit
- 框架：`Weex` `Webview`
- 客户端及版本：`B5.0+`
- 功能：编辑我的带看
- 参数：
  -`recordId String`
  -`type Number ` 0:  新增、1:  编辑、2:  删除、
- 响应：无

### commentNewHouse
- 框架：`Weex`
- 客户端及版本：`B5.2+`
- 功能：点评新房
- 参数：
  -`varNewCommunityId String`
- 响应：无

### commentHouseType
- 框架：`Weex`
- 客户端及版本：`B5.2+`
- 功能：点评户型
- 参数：
  -`typeId String`
- 响应：无

### switchTab
- 框架：`Weex`
- 客户端及版本：`B5.7+`
- 功能：切换tab
- 参数：
  -`position  int`
- 响应：无

### goContribute
- 框架：`Weex`
- 客户端及版本：`B5.7+`
- 功能：跳转投稿页
- 参数：
  -`url  String`
- 响应：无

### openSubsAgentQRCode
- 框架：`Weex`
- 客户端及版本：`B5.7+`
- 功能：行业动态详情页会员时，点击关注弹出二维码弹窗
- 参数：
  -`id  String`（经纪人id）
- 响应：无

### getCityCode
- 框架：`Weex`
- 客户端及版本：`C4.10+`
- 功能：发现页获取cityCode，以请求数据
- 参数：
  -无
- 响应：cityCode `String`


### showCommunityAgentList
- 框架：`Weex`
- 客户端及版本：`C5.0.0+`
- 功能：小区详情页-在线咨询小区经纪人
- 参数：
  - `id String`（小区ID communityId）
- 响应：
 - 无

### shareAnalystCard
- 框架：`Weex`
- 客户端及版本：`B6.15.0+`
- 功能：房产分析师分享名片
- 参数：
  - `analystInfo Object`（房产分析师的基本数据）
- 响应：
 - 无

### getSystemInfo
- 框架：`Weex` `Web`
- 客户端及版本：`T2.2.3+`
- 功能：获取设备参数
- 参数：
  - 无
- 响应：`SystemInfoType data`
```js
SystemInfoType = {
	statusBarHeight: Number, // 状态栏高度
}
```

### setStatusBarStyle
- 框架：`Weex` `Web`
- 客户端及版本：`T2.2.3+`
- 功能：获取设备参数
- 参数：`StyleType data`
```js
StyleType = {
	textStyle: String, // 字体样式 dark = 黑色，light = 白色
}
```
- 响应：
	- 无


###  shareAnalystInfo
- 框架：`Weex` `Web`
- 客户端及版本：`C5.3+`
- 功能：分享房产分析师信息
- 参数：`AnalystShareInfo data`
```js
AnalystShareInfo = {
  avatar: String	//头像
  name: String	//名称
  desc: String	//简介
  level: Number	//等级
  publishCount: Number	//发布数
  fansCount: Number	//粉丝数
  praiseCount: Number	//点赞数
  shareType: {	//分享信息
    appOriginalId: String
    path: String
    title: String
    description: String
    imageUrl: String
    webUrl: String
  },
}
```
- 响应：
	- 无


###  setTitleBackgroundColor 
- 框架：`Weex` `Webview`
- 客户端及版本：`C5.3+`
- 功能：设置标题栏背景色
- 参数：
  - `String backgroundColor`
- 响应：无

###  setStatusBar 
- 框架：`Weex` `Webview`
- 客户端及版本：`C5.3+`
- 功能：设置页面通知栏
- 参数：
  - `StatusInfo data`
- 响应：无

```
StatusInfo {
  backgroundColor: String  //通知栏背景色RGB值，例"F95E69"
}
```

###  getWifiInfo
- 框架：`Weex` `Webview`
- 客户端及版本：`B7.3.0+`
- 功能：获取设备当前Wi-Fi信息，无论是否连接Wi-Fi，都会返回`WifiInfo`内容，只有成功获取Wi-Fi信息才会返回`SSID`及`BSSID`
- 参数：无
- 响应：`WifiInfo`

```
WifiInfo {
  isConnect:Bool //如果未连接Wi-Fi，则SSID/BSSID都不会返回
  SSID:String //Wi-Fi名称
  BSSID: String  //AB:A3:8F:0B:C1:98，Wi-Fi的BSSID信息，可以认为是MAC地址，字母全部大写，使用「:」进行分割，iOS会进行补0操作以确保每一组都有两个字符
}
```

## Weex 独有接口

### getHost
- 框架：`Weex`
- 客户端及版本：`C1.7+` `B4.0+` `T1.0+`
- 功能：返回 Weex 服务域名，开发环境为 `https://weex.dev.kanfangjilu.com`，生产环境为 `https://www.xiaoluxuanfang.com`
- 参数：无
- 响应：`String`

### ready
- 框架：`Weex`
- 客户端及版本：`C1.7+` `B4.0+` `T1.0+`
- 功能：打开 Weex 页面时，直接显示过渡页面不显示 Weex 页面，当 Weex 页面就绪时，会调用 ready 接口，客户端隐藏过渡页面
- 参数：无
- 响应：无

### reload
- 框架：`Weex`
- 客户端及版本：`C1.7+` `B4.0+` `T1.0+`
- 功能：重新加载 Weex 页面（如同扫码调试时的左上角刷新按钮）
- 参数：无
- 响应：无

## WebView 独有接口

### showBottomScreenshotShare
- 框架：`WebView`
- 客户端及版本：`C2.1+`
- 功能：显示截图分享底栏
- 参数：
  - ScreenshotShareType data
- 响应：无

```
ScreenshotShareType {
  height: Number, // 截图的高度
  rotate: Number, // 截图旋转角度（顺时针），默认值 `0`，可能值为 `0` `90` `180` `270`
}
```

### generateCardImage
- 框架：`WebView`
- 客户端及版本：`B4.5+`
- 功能：截取名片信息
- 参数：
  - CardGenerateType data
- 响应：无

```
CardGenerateType {
  height: Number, // 截图的高度
  error: Number, // 0-无误 / 1-有误（需终止流程）
}
```

### getCard
- 框架：`WebView`
- 客户端及版本：`B4.5+`
- 功能：获取名片信息
- 参数：无
- 响应：`CardResultType`

```
CardResultType {
  name: String,
  phone: String,
  avatarDataUrl: String, // 格式类似 data:image/gif;base64,R0lGOD
  companyName: String,
  companyLogoDataUrl: String, // 格式同 avatarDataUrl
  jobTitle: String,
  shopAddress: String,
}
```

### getGroupCard
- 框架：`WebView`
- 客户端及版本：`B5.0+`
- 功能：获取小组名片信息
- 参数：无
- 响应：`GroupCardResultType`

```
GroupCardResultType {
  name: String,
  shopAddress: String,
  backgroundDataUrl: String, // 格式类似 data:image/gif;base64,R0lGOD
}
```

### purchaseProduct
- 框架：`WebView`
- 客户端及版本：`B5.9+`
- 功能：调起自建支付，支付结束后（不论成功失败取消），派发全局事件 `onPaymentResult(PaymentResultType)`
- 参数：
  - PurchaseType data
- 响应：无

```
PurchaseType {
  productId: String, // 商品 ID
  channel: String, // 支付渠道，同后端 API：wx / alipay
  agentId: String, // 经纪人 ID
}
```

## 已废弃接口

### 【B4.2废弃】postForumChat
- 框架：`Weex` `Webview`
- 客户端及版本：`B4.1+`
- 功能：社区提醒页面聊一下功能
- 参数：
  - `ForumChatType data`
- 响应：无，但要触发 `onNativeStatusUpdate.FORUM_CHAT`

```
ForumChatType {
  userId: String, // 消息的作者 ID
  userName: String, // 消息的作者昵称
  targetId: String, // 消息 ID
  costPoint: Number, // 是否需要扣除积分
  status: Number, // 能否聊一下状态
  check: {
    title: String, // 弹窗标题
    content: String, // 弹窗内容
    cancelText: String, // 取消文案
    confirmText: String, // 确认文案
    confirmNativeUrl: String, // 确认后跳转 URL
  },
  message: { // 可直接用于聊天消息的 ImageTextMessage
    title: String, 
    content: String,
    imageUrl: String,
    url: String,
    actionText: String,
    nativeUrl: String,
    extInfo: String,
  },
}
```

### 【C1.7废弃】showAgentBox
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：唤起经纪人信息框，点击空白处或按钮则直接销毁信息框，但点击底部订阅按钮派发全局事件 `onAgentBoxButtonClicked(AgentCallbackType)`，点击底部订阅按钮派发全局事件 `onAgentBoxButtonClicked(AgentCallbackType)`，点击底部取消订阅按钮派发全局事件 `onAgentBoxButtonClicked(AgentCallbackType)`，点击底部聊天按钮派发全局事件 `onAgentBoxButtonClicked(AgentCallbackType)`，点击底部电话按钮派发全局事件 `onAgentBoxButtonClicked(AgentCallbackType)`
- 参数：
  - `AgentType data`
- 响应：无

```
AgentType {
  id: String,
  avatar: String, // 经纪人头像
  vipLevel: Number, // 0-非会员 / 1-会员
  name: String, // 经纪人昵称
  description: String, // 经纪人描述（设计稿灰字部分）
  subscribed: Number, // 0-未订阅 / 1-已订阅
  phone: String,
  // 可能还会有其它字段
}

AgentCallbackType {
  type: Enum { "SUBSCRIBE", "UNSUBSCRIBE", "CHAT", "PHONE" },
  data: AgentType, // 可能字段会有所调整，所以需要原样回传 AgentType
}
```

### 【C1.7废弃】showInfoPage
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：显示信息页面，点刷新后信息页面能够消失
- 参数：
  - `InfoPageType data`
- 响应：无

```
InfoPageType {
  icon: String, // 信息页图标的图片地址（必填，可能是本地资源，也可能是网络图片）
  title: String, // 信息页标题（必填）
  description: String, // 信息页描述（选填）
}
```

### 【C1.7废弃】trackEvent
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+`
- 功能：上报友盟事件
- 参数：
  - `String eventId`
  - `Object eventData`
- 响应：无

### 【C1.7废弃】updateFeeds
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：触发订阅 tab 更新
- 参数：无
- 响应：无

## 事件（客户端调用 JS）

### onViewAppear
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：在再次进入页面时触发，比如打开了新页面后返回，又比如切换 Tab 到首页又回到订阅
- 参数：无
- 响应：无

### onViewDisappear
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：在离开页面时触发
- 参数：无
- 响应：无

### onParentTabAppear
- 框架：`Weex`
- 客户端及版本：`B6.18+`
- 功能：在父页面所在Tab可见时触发
- 参数：无
- 响应：无

### onActionClicked
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：当右上角按钮被点击
- 参数：
  - `ActionType data`
- 响应：无

### onActionSheetItemClicked
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+` `B4.0+` `T1.0+`
- 功能：当 Action Sheet 按钮被点击
- 参数：
  - `ActionSheetItemType data`
- 响应：无

### onVerticalActionSheetItemClicked
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.0+` `B4.0+` `T1.0+`
- 功能：当 Vertical Action Sheet 按钮被点击
- 参数：
  - `VerticalActionSheetItemType data`
- 响应：无

### onNativeStatusUpdate
- 框架：`Weex` `Webview`
- 客户端及版本：`C2.0+` `B4.0+` `T1.0+`
- 功能：通用事件，当客户端状态发生变化
- 参数：
  - `StatusUpdateType data`
- 响应：无

```
StatusUpdateType {
  type: String, // 类型
  id: String, // ID
  status: Number, // 状态位
  data: Object, // 传递数据
}
```

- 聊一下有变化 type=CHAT, id=xxx, status=1
- 社区问题有变化 type=FORUM_QUESTION, id=xxx, status=1
- 社区回答有变化 type=FORUM_ANSWER, id=xxx, status=1
- 社区评论有变化 type=FORUM_COMMENT, id=xxx, status=1
- 社区聊一下有变化 type=FORUM_CHAT, id=xxx, status=1
- 拉黑状态有变化 type=BLACKLIST, id=xxx, status=1（0-解除黑名单 / 1-加入黑名单）
- 客户主页刷新 type=CUSTOMER_UPDATE, id=xxx, status=1
- 客户主页刷新-解除关联 type=CUSTOMER_UNLINK, id=xxx, status=1 
- 客户主页刷新-通知【最近访客】页面刷新 type=CUSTOMER_UPDATE_VISITORS, id=xxx, status=1
- 客户主页刷新-删除客户返回上一页 type=CUSTOMER_DELETE, id=xxx, status=1
- 企业详情页面刷新 type=TEAMHOUSE,id=xxx,status=1
- 编辑我的带看后，带看详情页面刷新 type=EDIT_GUIDE_VISIT，id=xxx（recordId） 
- 楼评以及回复详情页面---回复评论之后，刷新weex界面 type=REPLY_COMMENT，id=xxx（recordId）
- 新房点评之后,新房详情、列表页面更新 type=NEWHOUSE_COMMENT,id=xxx,status=1 
- 户型点评之后,户型详情、列表页面更新 type=HOUSETYPE_COMMENT,id=xxx,status=1 
- 进入问题详情页（弹起分享） type=FORUM_OPEN_QUESTION_DETAIL, id=xxx, status=x （0-新建问题/1-回答问题）
- 楼市号自由编辑提交成功 type=EDIR_WRITING_SUCCESS,id=articleId,data={XXX:XXX} 传递接口返回的rsp

### onSubscriptionStatusUpdate
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.7+`
- 功能：当订阅状态发生变化（订阅或取消订阅）
- 参数：
  - `SubscriptionCallbackType data`
- 响应：无

```
SubscriptionCallbackType {
  itemType: String,
  itemId: String,
  subscribed: Number, // 0-未订阅 / 1-已订阅
}
```

### onUserUpdate
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：当用户登录态发生变化（登录、登出等情况）
- 参数：无
- 响应：无

### onPaymentResult
- 框架：`WebView`
- 客户端及版本：`B5.9+`
- 功能：当支付结束后（不论成功失败取消），派发全局事件（假如是用户主动回到 App，依旧派发取消事件）
- 参数：
  - PaymentResultType data
- 响应：无

```
PaymentResultType {
  result: String, // 支付结果：success / fail / cancel / invalid
  extra: String, // 支付平台返回的扩展信息
}
```

### openInputDialog
- 框架：`Weex` `Webview`
- 客户端及版本：`C3.0+`
- 功能：统一客户端回复弹窗
- 使用点：
	1.楼评底部添加评论框，参数：
		commentId: 评论id,
       		type: 'COMMENT',
	2.楼评回复详情页底部添加回复框，参数：
		name: 被回复人,
       		commentId: 回复id,
       		type: 'REPLY',
	3.楼评页评论列表点击评论进行回复，参数
		commentId: 当前评论id,
        	name：当前评论人名字
        	type: 'REPLY',
	4.楼评页评论列表点击回复进行回复，参数
		replyId: 回复id,
       		name: 回复人名字,
        	commentId: 评论id,
        	type: 'REPLY',
	5.楼评回复详情回复列表，回复--回复，参数：
		name: 被回复人,
          	commentId: 回复id,
          	type: 'REPLY',
	6.楼评回复详情回复列表，回复--回复的回复，参数：
		replyId: 回复的回复id,
          	name: 回复人名字,
          	commentId: 被回复的id,
          	type: 'REPLY',
	7.新房/户型点详情页，底部添加评论，参数：
          	commentId: 目标id,
          	type: 'NEW_HOUSE_COMMENT',
	8.经纪人之家动态 - 评论动态 参数：
          	id: 动态id,
          	type: 'AGENT_HOME_COMMENT',
	9.经纪人之家动态 - 回复动态的评论 参数：
          	id: 动态id,
		name: 被回复人,
		commentId：评论id,
          	type: 'AGENT_HOME_REPLY',
		tagId: 标记id - 纯传递 (当回复‘回复’时，去评论id，标记改评论的回复列表刷新)
	10.社区动态 - 评论动态 参数：
		commentId: 动态id,(评论成功后回传weex)
		name: 被评论人,
          	type: 'DYNAMIC_COMMENT'
	11.社区动态 - 回复动态的评论 参数：
		name: 被回复人,
		commentId：评论id,(评论成功后回传weex)
          	type: 'DYNAMIC_REPLY',
		replyId: 回复id

- 响应：无

### getCommonConfig
- 框架：`Weex`
- 客户端及版本: `T1.0+`
- 功能：获取签到相关全局配置信息（签到类型、店长评价、店员态度）
- 参数：无
- 响应：`Object`
```
result: {
	checkinType: [ConfigItem], // 签到类型
	checkinEmployerType: [ConfigItem], // 店长评价
	checkinEmployeeType: [ConfigItem], // 店员态度
},

ConfigItem = {
	key: String, // 选项唯一值
	name: String, // 选项文本
	children: [ConfigItem], // 子选项
}
```

### getAgentInfo
- 框架：`Weex` 
- 客户端及版本： `B5.9.0+` 
- 功能：返回用户基本信息
- 参数：无
- 响应：`Object`

```
result: {
	agentId：String, // 经纪人id
	isMLS: Number, // 1 MLS号
    showFavorite: String, // 投稿底部展示房源
}
```
### getUserInfo
- 框架：`Weex` 
- 客户端及版本： `C4.1.0+` 
- 功能：返回用户基本信息
- 参数：无
- 响应：`Object`

```
result: {
	uid：String, // 用户id
}
```

###  getUserLocation

- 框架：`Weex` 
- 客户端及版本： `B6.15.0+` 
- 功能：获取当前定位信息
- 参数：无
- 响应：`Object`


```
成功时
result: {
	state: 1
	adcode: String, // 行政区code
	cityAdCode: String //城市code
	lat: String, // 经度
	lng: String, // 纬度
}
失败时
result:{
	state: 0 // iOS为0时表示没有权限,自动弹出授权框, 安卓为0时表示弹出授权框后点击了取消授权
}
```

###  showCityPickerView

- 框架：`Weex` 
- 客户端及版本： `B6.15.0+` 
- 功能：城市选择器
- 参数：`Object`
- 响应：`Object` 定位失败或者拒绝权限时，返回空""

```
req: {
	adcode: String // 城市编
}

result: {
	adcode：String, // 城市code
	name: String // 城市名称

}
```

### openMiniProgram
- 框架：`Weex` 
- 客户端及版本： `B6.15.0+` 
- 功能：打开小鹿名片微信小程序、打开小鹿网店微信小程序
- 参数：`Object`
- 响应：无

```
req: {
	appOriginalId: String
    path: String 
}
```

###  `showInputView` 

- 框架：`Weex` 
- 客户端及版本： `B6.16.0+` 
- 功能：调起系统输入框
- 参数：`Object`
- 响应：`Object`

```
req: {
	title1 : String
	data1 : Number 
	unit1 : String 
	title2 : String
	data2 : Number 
	unit3 : String 
}

rsp: {
	title1 : String
	data1 : Number 
	unit1 : String 
	title2 : String
	data2 : Number 
	unit3 : String 
}
```
### `setBadge`

- 框架：`Weex` 
- 客户端及版本： `B6.18.0+` 
- 功能：设置多Tab页数量提醒角标
- 参数：`Object`
- 响应：无

###  `showCustomAlertView` 

- 框架：`Weex` 
- 客户端及版本： `B7.4.0+` 
- 功能：弹出自定义浮层提示框
- 参数：`Request`
- 响应：`Response`

```
Request: {
	title  : String
	message : String
	buttons: [
		{
			title: String
			cancel: Boolean (true 取消样式，默认灰色)
			color: String ('#191919', 十六进制颜色值) // 新增，若有将最终决定按钮文本颜色
		},
		...
		// 最多支持两个按钮（顺序从左到右）
	]
}
```
```
Response: {
	index: Number // 点击按钮的索引
	title: String  // 点击按钮的文本
}
```

## 已废弃事件

### 【C1.7废弃】onAgentBoxButtonClicked
- 框架：`Weex` `Webview`
- 客户端及版本：`C1.6+`
- 功能：当经纪人信息框按钮被点击
- 参数：
  - `AgentCallbackType data`
- 响应：无