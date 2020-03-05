## PageSelector

### 方法

#### goToPageSelect

- 跳转需要选择回调的页面

- 参数

  ```json
  {
    type: String,	 // 配置的唯一标识
    query: Object, // 跳转参数
  }
  ```

#### onPageSelect`require`

- 统一处理选中事件

- 参数

  ```json
  {
    type : String,
    data : Object,
  }
  ```

#### onPageGoBack`require`

- 统一处理返回事件

- 参数

  ```json
  {
    type : String
  }
  ```

  *无法处理`iOS`的侧滑和`Android`的物理返回键的事件*

## 用法

```js
import PageSelector from 'xxx/PageSelector'

function selecteTeamList() {
	PageSelector.goToPageSelect('XX', { id: '123' })
    .then({ result, data } => {
    if (result === 'SUCCESS') {
    	// do something ...  
    } else if (result === 'CANCEL') {
      // do something ...
    }
  })
}


// ====== 选择页面 ======
/// team.vue
import PageSelector from 'xxx/PageSelector'

// @ Require
function onItemClick(item: object) {
  PageSelector.onPageSelect('Enterprise',item)
}

function onTeamGoBack() {
	PageSelector.onPageGoBack('Enterprise')
}

```

### 内部处理

以选择企业为例，业务方使用goToPageSelect方法时，会同时注册相关channel，其内部实现如下

```json
function goToPageSelect() {
  new Promise((resolve, reject) => {
    // 注册选中Channel
    const teamReceiver = new BroadcastChannel('PAGE_SELECTOR_TEAM_LIST')
		teamReceiver.onmessage = (event) => {
			resolve({status : event.status , data: event.data})
			teamReceiver.close()
    }
    this.openWeexUrl('url://xxxx.team')
  })
}
```

页面选择器选择完后，调用对应的返回事件，还是以选择企业为例

```json
function onPageSelect(data: Object) {
  const onClick = new BroadcastChannel('PAGE_SELECTOR_TEAM_LIST')
	onClick.postMessage({ status: 'SUCCESS': data: data })  
}

function onPageGoBack() {
  const goback = new BroadcastChannel('PAGE_SELECTOR_TEAM_LIST')
	goback.postMessage({ status: 'CANCEL' })
}
```

