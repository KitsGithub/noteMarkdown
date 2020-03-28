# HTML容器 fg-web-view





### 方法

#### setContent

- 设置webView网页内容

- 原本的``<script></script>``标签请使用`<fg-script></fg-script>`代替

  ```js
  this.$ref.webView.setContent(`<html>...</html>`)
  ```

#### loadURL

- 定向webView的url *这个方法会覆盖setContent的内容，为第一优先级*

  ```js
  this.$ref.webView.loadURL(`http://www.baidu.com`)
  ```

  

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

  

 

#### 使用方式
```js
// Weex
vm.$refs.FjkWebView.setContent(`
    <html>
    	<head>
    		<meta charset="utf-8">
    		<title>FjkWebView Test</title>
    		<meta name="viewport" content="width=device-width, initial-scale=1">
    	</head>
    	<body>
		<button onclick="click()">Hello world!</button>
    	</body>
		<fg-script>
			document.addEventListener('chartReload', function (e) {
			  // e.data 保存着此次事件携带的数据，即 e.data === options
			});
			function click(){
			  native.fireWeexEvent('foo', { data: 'event datail'})
			}	
		</fg-script>
    </html>
`)

// 或者只设置body内的值，原生会自动拼接<html>标签
vm.$refs.FjkWebView.setContent(`
    <p>Hello world!</p>
`)
```





#### 注意事项

由于 [Vue 的安全机制](https://cn.vuejs.org/v2/guide/security.html#HTML-%E5%86%85%E5%AE%B9)，无法在Vue中直接注入html，会被框架转义。除非通过 Dom 的一些操作注入，但是 Weex 也不支持 Dom。  
所以在 `fg-web-view` 的 content 中我们使用 `<fg-script>...</fg-script>` 来绕考 Vue 对 html文本的转码。同时原生也需要在接收到 content 时，将 `<fg-script>...</fg-script>` 替换为  `<script>...</script>`

### dispatchEvent

#### 说明
Weex 通知 Web的方式。Weex 向 web 容器广播全局事件

#### 参数
```
/**
* dispatchEvent
* @param {String} eventName 事件名
* @param {Object} eventData 事件携带的数据
*/
```

#### 实现细节
原生层通过对Web容器注入以下JS逻辑，实现 Weex 向 web 容器广播全局事件
```js
document.dispatchEvent(new CustomEvent(${eventName}, { 'data': ${data} }))
```

#### 使用方式
```js
// Weex
vm.$refs.FjkWebView.dispatchEvent('chartReload', options)
```
```js
// Web
document.addEventListener('chartReload', function (e) {
  // e.data 保存着此次事件携带的数据，即 e.data === options
});
```

## web 网页与 Weex 的通讯
> web 通过全局对象 `FJK` 访问原生方法

### fireWeexEvent

#### 使用方式
```html
// Weex
<fjk-web-view ref="FjkWebView" @foo="handleWebviewEvent"> </fjk-web-view>
```
```js
// Web 会有全局对象 FJK
FJK.fireWeexEvent('foo', { data: 'event datail'})
```