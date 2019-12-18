#### <fg-selector />

##### API

| Props  | 说明       |        类型         | 默认值 | 版本 |
| :----- | :--------- | :-----------------: | :----: | :--: |
| query  | 筛选项列表 |      `Object`       |  `[]`  |  -   |
| config | 筛选项配置 | `Array[configItem]` |  `[]`  |  -   |

```json
// config 数据结构
configItem = {
  'key': 'weeklyActiveRate',
  'filterType': FilterType,
	'dataList': [FilterItem],	// 业务方自定义数据源。
  'ids': [String],	// 选中的key - ？ 复合类型的[[1,2],[1,2]] ？？
  'callBackChannel': 'AddressBook',	// 注册页面通讯管道 (用于具有回调属性的筛选项注册响应的管道)
}

FilterType =  'List', 'Filter', 'City'

// 筛选Item
FilterItem: {
  title: '周活跃率',
  cols: 3,			// 筛选项每行个数，default = 1 (FilterType = 'List'的时候固定为 1)
	single: true,	// 单选or多选 defalut = true
  disable: false, // 是否可以点击 defalut = false
  children: [FilterChildrenItem],
  
  /* WARNING: 待讨论 */
  limit: 1, 		// 多选模式下最大选择数 - 默认 Number.MAX_VALUE 
}

// 列表样式筛选项
FilterChildrenItem: {
	title: '不限',
  selected: false,	// 选中状态 - 内部数据处理,不对外暴露
  key: '0',
  isCallBack: false,	// 是否具有回调属性
}

// 城市选择器
// TODO: 
```

```vue
// 使用示例
<template>
	...
	<fg-selector :query="query" 
               :config="filterConfig"/>
</template>

<script>
  export default {
  	data() {
      return {
        query: {},
  			filterConfig: [
          {
            'key': 'employeeCount',
            'filterType': 'List',
            'ids': [],
            'dataList': [
              {
                title: '周活跃率',
                children: [
                  {
                    title: '不限',
                    key: '0',
                  },
                  {
                    title: '低于20%',
                    key: '1',
                  },
                  {
                    title: '20%-60%',
                    key: '2',
                  },
                  {
                    title: '超过60%',
                    key: '3',
                  },
                ],
              }
            ],
          },
        ]
    	}
  }
</script>
```

##### Events

`handelQuery`<font style="background:white; color:black">外部方法</font>

- 描述：改变配置项，需要一个返回值

- 参数：

  ```json
  {
    query: Object,	// 传入的query
    key: String,	// 当前筛选项配置的key
    filterList: Array[DataListItem], // 当前筛选项的数据
  }
  
  // 返回一个变更后的query
  retrun query
  ```

`onSelectedFinshed`<font style="background:white; color:black">外部方法</font>

- 描述：统一回调seleted

- 参数

  ```json
  {
    query: Object, // 选择完后的query
  }
  ```

`select`<font style="color:red">内部方法</font>

- 描述： 选中某个筛选项

- 参数：

  ```json
  {
    query: Object // 筛选后的
  }
  ```

`onCallBackSelectorItemClick` <font style="color:red">内部方法</font>

- 描述：具有回调属性的选项选中

- 参数

  ```json
  {
    key: 0, // 下标
    Item: Object, // 当前选中的筛选项
  }
  ```

`setSelecotrItemSelected()` <font style="color:red">内部方法</font>

- 设置某个筛选项为选中状态

- 参数：

  ```json
  {
    item: SelectorItem, // 需要设置为选中状态的Item
  }
  ```

#### <fg-selector-filter />

##### API

| PropsPorps         | 说明         | 类型                | 默认值        | 版本 |
| ------------------ | ------------ | ------------------- | ------------- | ---- |
| list               | 筛选项列表   | `Array[FilterItem]` | `[]`          | -    |
| backgroundColor    | 默认背景颜色 | `String`            | `#FFFFFF`     | -    |
| checkedColor       | 选中背景颜色 | `String`            | `#111111`     | -    |
| borderColor        | 默认边框颜色 | `String`            | `transparent` | -    |
| checkedBorderColor | 选中边框颜色 | `String`            | `transparent` | -    |

##### Events

`select`

- 说明：筛选项点击回调方法

- 参数

  ```json
  {
    key: '', // 当前Item.key
    item: FilterChildrenItem,
    checkedList: [FilterChildrenItem], // 当前筛选项所有选中的列表
  }
  ```

`onCallBackFilerItemClick`

- 说明：具有回调属性的Item方法

- 参数

  ```json
  {
    index: 0, // 下标
    item: ListFilterCildrenItem, // 当前筛选项
  }
  ```

`setSelecotrItemSelected()` — `保留意见`

- 设置某个筛选项为选中状态

- 参数：

  ```json
  {
    item: FilterChildrenItem, // 需要设置为选中状态的Item
  }
  ```



#### <fg-selector-list-filter />

与`<fg-selector-filter />`类似



#### <fg-selector-city-filter />

TODO：