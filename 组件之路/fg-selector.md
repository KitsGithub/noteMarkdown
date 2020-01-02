## fg-selector

### API

| Props | 说明       |   类型   | 默认值 | 版本 |
| :---- | :--------- | :------: | :----: | :--: |
| query | 筛选项列表 | `Object` |  `{}`  |  -   |

```js
// config 数据结构
ConfigItem: {
    name: String, // 选项文本
    key: String, // 选项唯一值
    // TAB 筛选类型：CITY = 城市选择器，DATE = 时间选择器，RADIO_LIST = 单选列表，FILTER_LIST = 复杂选择器，FILTER_RADIO = 复杂单选
    // FILTER_LIST 筛选类型： FILTER_RADIO = 复杂选择器-单选 FILTER_CHECKBOX = 复杂选择器-多选
    type: String,
    config: FilterConfig, // 筛选配置
    children: [ConfigItem],
}
    
// 区域选择
RegionItem = {
  name: String,
  level: String, // 区域级别：province，city，district，street 依次是 省，市，行政区，片区
  adcode: String, // 区域编码， 主键，唯一
  adcodeCity: String, // 当前区域的上级城市编码
  adcodeProvince: String, // 当前区域的上级省级编码
  cityName: String, // 当前区域的上级城市名称
  provinceName: String, // 当前区域的上级省级名称
}
  
// 筛选配置
FilterConfig = {
  cols: Number, // [FILTER_RADIO][FILTER_CHECKBOX] 单行展示{clos}个筛选项
  min: Number, // [FILTER_RADIO][FILTER_CHECKBOX] 可被勾选的 checkbox 的最大数量（大于等于0）
  max: Number, // [FILTER_CHECKBOX] 可被勾选的 checkbox 的最大数量（大于等于1）
  channel: 'DEPARTMENT'
}

// 例子
config = [
      {
          name: '城市',
          key: 'cityCode',
          type: 'CITY'
      },
      {
          name: '筛选',
          key: 'filter',
          type: 'FILTER_LIST', 
          children: [
              {
                  name: '角色',
                  key: 'roleType',
                  type: 'RADIO_LIST',
                  // 单选(FILTER_RADIO 默认)
                  handelSelected: function (query, ConfigItem) {
                      ...
                      retrun query
                  },
                  // 多选(FILTER_CHECKBOX 默认)
                  handelSelected: function (query, [ConfigItem]) {
                      ...
                      retrun query
                  },
                  handelSelected: function (query, RegionItem) {
                      ...
                      retrun query
                  },                
                  // 时间选择
                  handelSelected: function (query, timestamp) {
                      ...
                      retrun query
                  },
                  children: [
                      {
                          name: '不限',
                          key: '0',
                      }, {
                          name: '20人内',
                          key: '1',
                      }, {
                          name: '20-50人',
                          key: '2',
                      },
                  ],
              }
          ],
      }
]
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
        query: {
          cityCode: '',
          RoleType: '',
        },
        filterConfig: [
          // 城市选择器
          {
          	name: '城市',
          	key: 'cityCode',
         	  type: 'CITY'
      		},
          // 单选列表选择器
          {
            name: '选择角色',
          	key: 'RoleType',
          	type: 'RADIO_LIST', 
            children: [
              {
                name: '全部',
                key: '0',
              },
              {
                name: '上级',
                key: '1',
              },
              {
                name: '员工',
                key: '2',
              },
            ]
          }
        ]
    	}
  }
</script>
```

### 参数说明

| 参数             | 说明                                      | 值                                       | 默认值       |
| ---------------- | ----------------------------------------- | ---------------------------------------- | ------------ |
| `type`           | 筛选类型：                                | `CITY`/`DATE`/`RADIO_LIST`/`FILTER_LIST` | `RADIO_LIST` |
|                  | `FILTER_LIST`又包含2个子项                | `FILTER_RADIO`/`FILTER_CHECKBOX`         | -            |
| `FilterConfig`   | 描述筛选项配置**仅在`FILTER_LIST`下生效** |                                          |              |
| `cols`           | 列数                                      | `Number`                                 | 3            |
| `min`            | 最小选择数                                | `Number`                                 | 0            |
| `max`            | 最大选择数                                | `Number`                                 | `MAX_VALE`   |
| `channel`        | **注册通讯管道**                          | `String`                                 | -            |
| `handelSelected` | 设置qurey变化方法                         | `Function`                               |              |

- 如果是DATE的类型的时候，对应的key值应为毫秒级别的时间戳

### Events

#### 外部方法

`setConfigs`

- 描述：设置选择器的config

```js
this.$ref.selector.setConfigs([ConfigItem])
```

##### `onSelectedFinshed`

- 描述：统一回调seleted

- 参数

  ```js
  {
    query: Object, // 选择完后的query
  }
  ```

`OnChannelSelected`

- 描述：：当配置项里设置了`channel`字段时，当这个选项被选中，会调用这个方法

- 参数

  ```json
  {
    channel: 'DEPARTMENT'
  }
  ```