#### <fg-selector />

##### API

| Props  |         说明         | 类型             | 默认值 | 版本 |
| ------ | :------------------: | ---------------- | ------ | ---- |
| list   |      筛选项列表      | `selectorItem[]` | `[]`   | -    |
| height | 选择器高度，单位`px` | `Number`         | 90     | -    |

| Slots | 说明     |
| :---- | -------- |
| image | 右侧图片 |

##### Events

`select`

- 描述： 选中某个筛选项

- 参数：

  ```json
  {
    index: 0, // 下标
    item: selectorItem, // 当前选中的筛选项
  }
  ```

`onCallBackSelectorItemClick`

- 描述：具有回调属性的选项选中

- 参数

  ```json
  {
    index: 0, // 下标
    Item: Object, // 当前选中的筛选项
  }
  ```



`setSelecotrItemSelected()` — `保留意见`

- 设置某个筛选项为选中状态

- 参数：

  ```json
  {
    item: selectorItem, // 需要设置为选中状态的Item
  }
  ```

`getData()`

- 获取当前筛选项所有Data

- 参数：

  ```json
  {
    list : [selectorItem]
  }
  ```

##### 数据结构

```json
{
 	[selectorItem] 
}

// 选择器Iitem
selectorItem: {
  title: '周活跃率'， 		 // 标题
  isCallBack: false, 		// 是否具有回调属性
  selected: false,			// 选中状态 - 内部数据处理,不对外暴露
  disable: false,				// 不可选中状态
  Children: [FilterChildrenItem],	// 子筛选项
}

// List Style Children Item
// 筛选样式
FilterChildrenItem: {
  title: '时长情况',
  key: '0',
  cols: 3,			// 筛选项每行个数
	single: true,	// 单选or多选
  children: [ListFilterCildrenItem],
  
  /* WARNING: 待讨论 */
  limit: 1, 		// 多选模式下最大选择数 - 默认 Number.MAX_VALUE 
}

// 列表样式筛选项
ListFilterCildrenItem: {
	title: '不限',
  selected: false,	// 选中状态 - 内部数据处理,不对外暴露
  key: '0',
  isCallBack: false,	// 是否具有回调属性
}

// 城市选择器
// TODO: 

```



#### <fg-selector-filter />

##### API

| PropsPorps         | 说明         | 类型                   | 默认值        | 版本 |
| ------------------ | ------------ | ---------------------- | ------------- | ---- |
| list               | 筛选项列表   | `FilterChildrenItem[]` | `[]`          | -    |
| backgroundColor    | 默认背景颜色 | `String`               | `#FFFFFF`     | -    |
| checkedColor       | 选中背景颜色 | `String`               | `#111111`     | -    |
| borderColor        | 默认边框颜色 | `String`               | `transparent` | -    |
| checkedBorderColor | 选中边框颜色 | `String`               | `transparent` | -    |

##### Events

`select`

- 说明：筛选项点击回调方法

- 参数

  ```json
  {
    index: 0, // 当前下标
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