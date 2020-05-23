# HTML容器 fg-web-view

### Methods

### setHeight

- 说明：Weex 设置 WebView 容器高度，单位 px。不设置的话，默认无高度。

  ```js
  // Weex
  @param {Number} value 容器高度
  vm.$refs.FjkWebView.setHeight(300)
  ```

### setWidth

- 说明：Weex 设置 WebView 容器宽度，单位 px。不设置的话，默认满宽

  ```js
  // Weex
  @param {Number} value 容器宽度
  vm.$refs.FjkWebView.setWidth(750)
  ```

#### setContent

- 设置webView网页内容

- 原本的``<script></script>``标签请使用`<fg-script></fg-script>`代替

  ```js
  this.$ref.webView.setContent(`<html>...</html>`)
  ```

##### 使用方式

```js
// Weex
// 只需要设置body内容，预设的html模版会内置Echart、Promise
vm.$refs.FjkWebView.setContent(`
    <p>Hello world!</p>
    <fg-script>...</fg-script>
`)
```

#### setContentWithURL

- 定向webView的url

  ```js
  this.$ref.webView.setContentWithURL(`http://www.baidu.com`)
  ```



*组件内部网页加载生命周期*

#### onWebViewStartLoad

- 网页开始加载

  ```vue
  <template>
    <fg-web-view @onWebViewStartLoad="onWebViewStartLoad"/>
  </template>
  
  <script>
    //...
    methods: {
      onWebViewStartLoad() {
  			// do something
      }
    }
  </script>
  ```

#### onWebLoadFinished

- 网页加载完成回调

- 参数

  - `height`  当前webView加载的实际高度
  - `width`  当前webView实际的宽度

  ```vue
  <template>
    <fg-web-view @onWebLoadFinished="onWebLoadFinished"/>
  </template>
  
  <script>
    //...
    methods: {
      onWebLoadFinished(data) {
        console.log('data', JSON.stringify(data))
      }
    }
  </script>
  ```

#### onWebViewLoadFaild

- 网页加载失败回调

- 参数

  - `errorMessage` 失败原因

  ```vue
  <template>
    <fg-web-view @onWebViewLoadFaild="onWebViewLoadFaild"/>
  </template>
  
  <script>
    //...
    methods: {
      onWebViewLoadFaild(error) {
        console.log('error', JSON.stringify(error))
      }
    }
  </script>
  ```




#### 注意事项
由于 [Vue 的安全机制](https://cn.vuejs.org/v2/guide/security.html#HTML-%E5%86%85%E5%AE%B9)，无法在Vue中直接注入html，会被框架转义。除非通过 Dom 的一些操作注入，但是 Weex 也不支持 Dom。  
所以在 `fg-web-view` 的 content 中我们使用 `<fg-script>...</fg-script>` 来绕开 Vue 对 html文本的转码。同时原生也需要在接收到 content 时，将 `<fg-script>...</fg-script>` 替换为  `<script>...</script>`

#### 预设 HTML 模版
http://192.168.2.100/geek/trident-weex/blob/2bc3de8f60163b0270a3b8d47e4d13ef0ca9ab68/webViewTemplate.html 

### dispatchEvent

#### 说明
Weex 通知 Web的方式。Weex 向 web 容器广播全局事件

#### 参数
```
@param {String} eventName 事件名
@param {Object} eventData 事件携带的数据
```

#### 使用方式
> 假设场景：Weex端向Web端派发了一个名为`foo`的事件，并且携带数据 `{ bar: 'event datail'}`

```js
// Weex
vm.$refs.FjkWebView.dispatchEvent('foo', { bar: 'event datail'})
```
```js
// Web
document.addEventListener('foo', function (e) {
  // e.data 保存着此次事件携带的数据
  console.log(e.data) // { bar: 'event datail'}
});
```
```js
// 原生层通过对Web容器注入以下JS逻辑，实现 Weex 向 web 容器广播全局事件
(function(){var __internalEvent__ = new Event("foo");__internalEvent__.data = { bar: "event datail"};document.dispatchEvent(__internalEvent__);__internalEvent__ = null;})()
```

## Web 网页与 Weex 的通讯

### 全局对象 FJK

#### 说明
Web 调用原生方法，必须通过 WebView 全局对象 `FJK`，全局对象 `FJK` 由原生在加载WebView之前注入，所有原生方法都挂载在上面。

#### Android实现细节
```js
// 原生通过初始化JavaScript实例跟web通讯，T端JavaScript实例命名为 __FjkTridentWebViewJavascriptBridge__
// 原生初始化 Webview 之前前注入封装 FJK 的代码
// 由于 Web 和原生通讯本质都是通过 JSON 字符串，所有 FJK 在调用原生方法前，都需要将参数格式化成字符串，原生接收参数之后需要反序列化。
// 以下为部分核心代码，详细实现细节，见预设的HTML模版
(function (window) {
  const _nativeBridge = window.__FjkTridentWebViewJavascriptBridge__

  function fireWeexEvent(eventName, eventData) {
    _nativeBridge.fireWeexEvent(JSON.stringify(eventName), JSON.stringify(eventData))
  }

  window.FJK = {
    fireWeexEvent: fireWeexEvent,
  }
})(window)
```

### setHeight

#### 说明
Web 设置 WebView 容器高度，单位 px。不设置的话，默认无高度。

#### 参数
```
@param {Number} value 容器高度
```

#### 使用方式
```js
// Web
FJK.setHeight(300)
```

### setWidth

#### 说明
web 设置 WebView 容器宽度，单位 px。不设置的话，默认满宽

#### 参数
```
@param {Number} value 容器宽度
```

#### 使用方式
```js
// Web
FJK.setWidth(750)
```

### fireWeexEvent

#### 说明
Web 通知 Weex 的方式。Web 向 Weex 组件广播事件

#### 参数
```
@param {String} eventName 事件名
@param {Object} eventData 事件携带的数据
```

#### 使用方式
```js
// Web
FJK.fireWeexEvent('foo', { data: 'event datail'})
```
```html
// Weex
<fjk-web-view ref="FjkWebView" @foo="handleWebviewEvent"> </fjk-web-view>
```