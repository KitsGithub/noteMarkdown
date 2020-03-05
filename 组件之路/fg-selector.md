## fg-selector

- 日志
  - 新增`ALL_REGION`类型，筛选任意级别的省市区
  - `FilterConfig`新增 `isDisable` 配置项， `ConfigItem`新增`rule`字段补全反向映射关系
  - 完成基础功能
- TODO
  - [ ] `ConfigItem`没有默认值，默认值不应高亮
  - [ ] `isDisable`仅在`TAB`下生效，未适用到其他子集Item
  - [ ] `FilterConfig`的`channel`字段仅适用到`FILTER_CHECKBOX`下使用，且一个选择器仅能配置一个`channel`字段
  - [ ] `ConfigItem`新增的`rule`字段仅在`FILTER_LIST`下生效，未扩展到其他筛选项
  - [ ] 在安卓下，`fg-selector`的 `destory`方法不生效

### API

| Props | 说明       |   类型   | 默认值 | 版本 |
| :---- | :--------- | :------: | :----: | :--: |
| query | 筛选项列表 | `Object` |  `{}`  |  -   |

```js
// config 数据结构
ConfigItem: {
    name: String, // 选项文本
    key: String, // 选项唯一值
    // TAB 筛选类型：CITY = 城市选择器，ALL_REGION = 筛选区域，DATE = 时间选择器，RADIO_LIST = 单选列表，FILTER_LIST = 复杂选择器
    // FILTER_LIST 筛选类型： FILTER_RADIO = 复杂选择器-单选 FILTER_CHECKBOX = 复杂选择器-多选 FILTER_CUSTOM 自定义选择器
    type: String,
    config: FilterConfig, // 筛选配置
    children: [ConfigItem],
    rule: Object, // 如: {"order": "checkinCount", "sort": "asc"}
    // 单选(FILTER_RADIO 默认)
    handelSelected: function (query, ConfigItem) {
        ...
        return query
    },
    // 多选(FILTER_CHECKBOX 默认)
    handelSelected: function (query, [ConfigItem]) {
        ...
        return query
    },
    // 选择城市 [CITY]
    handelSelected: function (query, RegionItem) {
        ...
        return query
    },                
    // 时间选择 [DATE]
    handelSelected: function (query, timestamp) {
        ...
        return query
    },
}
    
// 区域选择
RegionItem = {
  level: String, // 区域级别：province，city，district，street 依次是 省，市，行政区，片区
  adcode: String, // 区域编码， 主键，唯一
  name: String,		// 当前选中的名字
  // 下面的字段用来描述上面的adcode
  adcodeCity: String, // 当前区域的上级城市编码
  cityName: String, // 当前区域的上级城市名称
  adcodeProvince: String, // 当前区域的上级省级编码
  provinceName: String, // 当前区域的上级省级名称
  adcodeArea: String,			// 行政区编码
  areaName: String,				// 行政区名称
  adcodeSubArea: String,	// 片区编码	
  subAreaName: String,		// 片区名称
}
  
// 筛选配置
FilterConfig = {
  cols: Number, // [FILTER_RADIO][FILTER_CHECKBOX] 单行展示{clos}个筛选项
  min: Number, // [FILTER_RADIO][FILTER_CHECKBOX] 可被勾选的 checkbox 的最大数量（大于等于0）
  max: Number, // [FILTER_CHECKBOX] 可被勾选的 checkbox 的最大数量（大于等于1）
  channel: 'DEPARTMENT',
  isDisable: Function isDeisabel(query) {	// 配置当前筛选项在什么条件下不可选中
    ...
    retrun true
  }
}
```

### 参数说明

| 参数             | 说明                                      | 值                                                    | 默认值       |
| ---------------- | ----------------------------------------- | ----------------------------------------------------- | ------------ |
| `type`           | 筛选类型：                                | `CITY`/`ALL_REGION`/`DATE`/`RADIO_LIST`/`FILTER_LIST` | `RADIO_LIST` |
|                  | `FILTER_LIST`又包含3个子项                | `FILTER_RADIO`/`FILTER_CHECKBOX`/`FILTER_CUSTOM`      | -            |
| `FilterConfig`   | 描述筛选项配置**仅在`FILTER_LIST`下生效** |                                                       |              |
| `cols`           | 列数                                      | `Number`                                              | 3            |
| `min`            | 最小选择数                                | `Number`                                              | 0            |
| `max`            | 最大选择数                                | `Number`                                              | `MAX_VALE`   |
| `isDisable`      | 当前tab是否可选状态                       | `Fuction`                                             |              |
| `channel`        | **注册通讯管道**                          | `String`                                              | -            |
| `handelSelected` | 设置qurey变化方法                         | `Function` 返回变化后的 `query`                       | -            |
| `handleDisable`  | 配置选择器是否可以点击状态                | `Function` 返回 `true`/`false`                        | -            |
|                  |                                           |                                                       |              |

- 如果是DATE的类型的时候，对应的key值应为毫秒级别的时间戳
- 当传入Tab的类型为 `ALL_REGION`时，对应`query`的字段应为一个对象，包含 `adcode`和`level`两个字段。选择完毕后会返回一个`RegionItem`对象

### Events

#### 外部方法

#####`setConfigs`

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

##### `updataConfig`

- 描述：更新单一配置项

- 参数

  ```json
  {
    config: ConfigItem
  }
  ```

##### `OnChannelSelected`

- 描述：：当配置项里设置了`channel`字段时，当这个选项被选中，会调用这个方法

- 参数

  ```json
  {
    channel: 'DEPARTMENT'
  }
  ```

### 使用示例

```js
// 单选列表选择器配置
let singleConfig = {
  name: '单选列表',
  key: 'RoleType',
  type: 'RADIO_LIST', 
  children: [
    {
      name: '列表一',
      key: '0',
    },
    {
      name: '列表二',
      key: '1',
    },
    {
      name: '列表三',
      key: '2',
    },
  ]
}
```

```json
// 城市选择器配置 - 数据报表中的城市选择
let cityConfig = {
  name: '城市',
  key: 'cityCode',
  type: 'CITY'
}
```

```js
// 选择时间配置
let dateConfig = {
  name: '选择日期',
  key: 'date',
  type: 'DATE',
}
```

```js
// 选择任意省、市、区、片区 的选择器
let query = {
  ...
  regionItem: {
    adcode: '100000' // 必填字段
  }
}
let regionConfig = {
  name: '选择城市',
  key: 'regionItem',
  type: 'ALL_REGION',
}
// 当确定选中某个Item时，会返回一个`RegionItem`,业务方需要定制自己的业务逻辑
```

```js
// 复杂选择器的配置
let filterConfig = {
  name: '筛选',
 	key: 'filter', // 这里只要保证外层的key唯一即可，无需在query中体现
  type: 'FILTER_LIST',
  children: [
    {
      name: "员工人数",
      key: 'employeeCountType', // 需要与 query 中配置的key一致， 否则不生效
      type: 'FILTER_RADIO', // 配置当前 '员工人数' 为单选
      children: [
        {
          name: '0~50人',
          key: 1,
        },
        {
          name: '50~199人',
          key: 2,
        },
        {
          name: '200~499人',
          key: 3,
        },
        {
          name: '500人以上',
          key: 4,
        },
      ]
    },
    {
      name: '重点跟进',
      key: 'focusType',
      type: 'FILTER_CHECKBOX', // 配置当前 '重点跟进' 为多选态
      children: [
        {
          name: 'A类',
          key: 3,
        },
        {
          name: 'B类',
          key: 2,
        },
        {
          name: 'C类',
          key: 1,
        },
      ]
    },
    // 特殊筛选 - 适用于某个筛选项是由多个query条件组合而成的, 如: 排序和筛选字段
    {
      name: '签到排序',
      key: 'sortByCheckIn',	// 这里的key 也无需在qurey 中体现，仅保持唯一性即可
      type: 'FILTER_CUSTOM',
      children: [
        {
          name: '最近签到',
          rule: {
            sort: 'modifyTime',
            order: 'desc',
          }
        },
        {
          name: '最近签到',
          rule: {
            sort: 'checkInTime',
            order: 'desc',
          },
        },
      ]
    },
    {
      name: '时间排序',
      key: 'sortByTime',
      type: 'FILTER_CUSTOM',
      children: [
        {
          name: '最近更新',
          rule: {
            sort: 'modifyTime',
            order: 'desc',
          }
        },
        {
          name: '最近签到',
          rule: {
            sort: 'checkInTime',
            order: 'desc',
          }
        }
      ]
    }
  ]
}
```