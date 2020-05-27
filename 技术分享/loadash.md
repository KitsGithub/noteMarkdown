

## Lodash 的使用

```vue
// 基本结构
<template>
  <div>
    <button title="排序" @click="onSortClick" />
    <div v-for="(object, index)" in dataSourceList :key="index" @click="onCellClick(object)">
      <text>{{ object.text }}</text>
    </div>
  </div>
<template>

<script>
/*
result = {
  result: [
    {
      "text": "标题",
      "isHot": true,
      "createTime": 1590204594598,
      "cityCode": 10000,
      "id" : '123',
    }], // 默认是空数组
    nextId: "1", // 如果没有下次请求，则无nextId字段
}
query = {
  keyword: '',
  nextId: '',
}
*/
servce.requireNetwork(query)
  .then((result) => {
    // do something ...
  
  })
</script>
```



### 高频使用方法

### `_.get(object, path, [defaultValue])`

```vue
<script>
servce.requireNetwork(query)
  .then((result) => {
    // 一般写法
    let nextId = result.nextId
    // nextId 的值可能是: undefined, "1"
    
    // lodash写法
    let nextId = _.get(result, "nextId", "")
    // nextId 的值可能是: "", "1"
  })
</script>
```

  

### `_.assign(object, [sources])`

> 来源对象的应用规则是从左到右，随后的下一个对象的属性会覆盖上一个对象的属性。

```vue

<script>
servce.requireNetwork(query)
  .then((result) => {
    let resultList = _.get(result, "result", [])
     for (let i = 0; i < resultList.length; i++) {
     let object = resultList[i]
     // 一般写法
     object.text = `**${object.text}**`
     // lodash写法1
     _.assign(object, { text: `**${object.text}**` })  // 会改变原有对象
     // lodash写法2
     let newObject = _.assign({}, object, { text: `**${object.text}**` }) // 不会改变原有对象
   }
   this.dataSourceList = resultList
})
</script>
```

  

### `_.map(collection, [iteratee=_.identity])`

```vue
// 还是上面的例子
<script>
servce.requireNetwork(query)
  .then((result) => {
    // 一般写法
    let resultList = _.get(result, "result", [])
   	for (let i = 0; i < resultList.length; i++) {
      let object = resultList[i]
      object = _.assign({}, object, { text: `**${object.text}**` })
    }
  
    // lodash写法
    let resultList = _.get(result, "result", [])
  	resultList = _.map(resultList, (object, index) => {
      return _.assign({}, object, { text: `**${object.text}**` })
    })
    
    // 结合 _.get
    const resultList = _.map(_.get(result, "result", []), (object, index) => {
      return _.assign({}, object, { text: `**${object.text}**` })
    })
    
    // 链式
    const resultList = _.chain(result)
      .get("result", [])
      .map((object) => {
      	return _.assign({}, object, { text: `**${object.text}**` })
      })
      .value()
    
      this.dataSourceList = resultList
})
</script>
```



### `_.filter(collection, [predicate=_.identity])`

```vue
<script>
// 需求: 点击排序按钮的时候，只isHot = ture 的数据
onSortClick() {
    // 只展示 isHot = true 的数据
    // 一般写法
    let hotList = []
    for (let i = 0; i < resultList.length; i++) {
      let object = resultList[i]
      if (_.get(object, "isHot", false)) {
        hotList.push(object)
      }
    }

    // lodash写法
    const hotList = _.filter(resultList, (object, index) => {
      return _.get(object, "isHot", false)
    })

    this.dataSourceList = hotList
}
</script>
```



### `_.find(collection, [predicate=_.identity], [fromIndex=0])`

> 遍历 `collection`（集合）元素，返回 `predicate`（断言函数）第一个返回真值的第一个元素。predicate（断言函数）调用3个参数： *(value, index|key, collection)*。

```vue
<script>
  // 需求: 点击cell的时候，判断 id 是否在某个特定数组里面
  onCellClick(object) {
    let defaultIds = ["aaa","bbb","ccc"]
    
    // 一般写法
    let findId = ""
    for (let i = 0; i < defaultIds.length; i++) {
      let defaultId = defaultIds[i]
      if (object.id === defaultId) {
        findId = object.id
        break
      }
    }
    if (findId.length) {
      // 找到某个id
    } else {
      // 找不到某个id
    }
    
    // lodash写法
    if (_.find(defaultIds, (defaultId) => defaultId === object.id) !== undefined) {
      // 找到某个id
    } else {
      // 找不到某个id
    }
    
  }
</script>
```



### `_.orderBy(collection, [iteratees=[_.identity]], [orders])`

> 此方法类似于 [`_.sortBy`](https://www.lodashjs.com/docs/latest#sortBy)，除了它允许指定 iteratee（迭代函数）结果如何排序。 如果没指定 `orders`（排序），所有值以升序排序。 否则，指定为"desc" 降序，或者指定为 "asc" 升序，排序对应值。

```vue
<script>
// 需求: 点击排序按钮的时候，需要按创建时间顺序/倒叙排列
servce.requireNetwork(query)
  .then((result) => {
    let resultList = _.map(_.get(result, "result", []), (object) => {
      return _.assign({}, object, { text: `**${object.text}**` })
    })
    // 按创建时间倒序(冒泡)
    for (var i = 0; i < resultList.length - 1; i++) {
        for (var j = 0; j < resultList.length - 1 - i; j++) {
            if (resultList[j].createTime > resultList[j + 1].createTime) {
                var temp = resultList[j];
                resultList[j] = resultList[j + 1];
                resultList[j + 1] = temp;
            }
        }
    }
  
    // lodash 写法
    // 倒序
    resultList = _.orderBy(resultList, "createTime", 'desc')
    // 顺序
    resultList = _.orderBy(resultList, "createTime", 'asc')
    
    this.dataSourceList = hotList
  })

</script>
```



### `_.groupBy(collection, [iteratee=_.identity])`

> 创建一个对象，key 是 `iteratee` 遍历 `collection`(集合) 中的每个元素返回的结果。 分组值的顺序是由他们出现在 `collection`(集合) 中的顺序确定的。每个键对应的值负责生成 key 的元素组成的数组。iteratee 调用 1 个参数： *(value)*。

```vue
<script>
servce.requireNetwork(query)
  .then((result) => {
    let resultList = _.map(_.get(result, "result", []), (object) => {
      return _.assign({}, object, { text: `**${object.text}**` })
    })
    
    // 新需求：需要数据展示按创建时间顺序，且按城市聚合数据
    /* 类似于下图的一个结构
    	----
    	城市A
    		---
    		cell - 2020-05-23
        cell - 2020-05-22
    		---
    	---
    	城市B
    		---
    		cell - 2020-05-23
        cell - 2020-05-22
    		---
    	---
    */
    /* 
    [
    	{ cityCode: '1', createTime: 4 }, 
    	{ cityCode: '2', createTime: 3 }, 
    	{ cityCode: '1', createTime: 2 }, 
    	{ cityCode: '2', createTime: 1 }
    ]
    */
    // 一般写法
    // 先把时间戳格式化出来
    resultList = _.map(resultList, (object) => {
      return _.assign(object, { 
        formatTime : new Date(object.createTime).format("YYYY-MM-DD") 
      })
    })
  	// 归类cityCode
    let cityArray = []
    for (let i = 0; i < resultList.length; i++) {
      let object = resultList[i]
      let targetCity = _.find(cityArray, (cityOjbect) => { cityOjbect.cityCode === object.cityCode }) !== undefined
      if (targetCity != undefined) {
        targetCity.children.push(object)
      } else {
        cityArray.push({
          cityCode: object.cityCode,
          children: [object]
        })
      }
    }
    /* cityArray = [
      {
        "cityCode": "1",
        "children": [
          {
            "cityCode": "1",
            "createTime": 4
          },
          {
            "cityCode": "1",
            "createTime": 2
          }
        ]
      },
      {
        "cityCode": "2",
        "children": [
          {
            "cityCode": "2",
            "createTime": 3
          },
          {
            "cityCode": "2",
            "createTime": 1
          }
        ]
      }
    ]
    */
    // 每个城市组再排序时间
    cityArray = _.map(cityArray, (cityObject) =>{
      return _.orderBy(cityObject.children, 'createTime', 'asc')
    })
    resultList = cityArray
    /*
    resultList = [
      [
        {"cityCode": "1","createTime": 2},
        {"cityCode": "1","createTime": 4}
      ],
      [
        {"cityCode": "2","createTime": 1},
        {"cityCode": "2","createTime": 3}
      ]
    ]
    */
  	
  
    // lodash 写法
    let cityArray = _.groupBy(resultList, 'cityCode')
    /* cityArray = {
        "1":[{"cityCode":"1","createTime":4},{"cityCode":"1","createTime":2}],
        "2":[{"cityCode":"2","createTime":3},{"cityCode":"2","createTime":1}]
    	}
    */
    cityArray = _.map(cityArray, (cityObject) =>{
      return _.orderBy(cityObject, 'createTime', 'asc')
    })
    /* cityArray = [
  	[
  	  {"cityCode":"1","createTime":2},
  	  {"cityCode":"1","createTime":4}
  	],
  	[
  	  {"cityCode":"2","createTime":1},
  	  {"cityCode":"2","createTime":3}
  	]
    ]
    */
    resultList = cityArray
    this.dataSourceList = resultList
})
</script>
```



- 复杂操作（链式）

```vue
<script>
servce.requireNetwork(query)
  .then((result) => {
    // 综合上面所有需求
    let resultList = _.chain(result)
      .get("result", [])
      .map((object) => {
      	return _.assign({}, object, { text: `**${object.text}**` })
      })
      .groupBy('cityCode')
      .map((cityObject) => {
        return _.orderBy(cityObject, 'createTime', 'asc')
      })
      .value()
  	
    /*
    resultList = [
  	[
  	  {"cityCode":"1","createTime":2},
  	  {"cityCode":"1","createTime":4}
  	],
  	[
  	  {"cityCode":"2","createTime":1},
  	  {"cityCode":"2","createTime":3}
  	]
    ]
    */ 
  
})
</script>
```



## Promise 介绍 

### 实例方法 `new Promise`

```js
function promis1() {
  return new Promise((resolve, reject) => {
    // dosomting ...
    // success
    if (success) {
      successData = {}, // 成功后需要返回的数据结构
      resolve(successData)
    } else {
      reject(new Error({ message: 'errorMessage' }))
    }
  })
}

// 使用
// fjkmodule.showLoading()
promis1()
  .then((success) => {
    // success
  })
  .catch((error) => {
    // error
  })
  .finally(() => {
    // 常用于loading框的销毁
    // fjkmodule.hideLoading()
  })
```



### 并行

#### `Promise.all([])`

> promise数组顺序与返回result的顺序是一致的  

```js
// 基本语法
Promise.all([
  promise1,
  promise2,
]).then((result) => {
  // do somthing
  // result = [ promiseResult1, promiseResult2 ]
}).catch((error) => {
  // 其中一个promise失败了都会进这个方法
}).finally(() => {
  // do something
})

// 等价于
Promise.all([
  promise1,
  promise2,
]).then(([ promiseResult1, promiseResult2 ]) => {})
```



#### `Promise.props({})`

> 定义的promise结构与返回的数据结构是一致的  

```js
Promise.props({
  promiseResult1 : promise1,
  promiseResult2 : promise2,
}).then((result) => {
  // do something
  // result = { promiseResult1, promiseResult2 }
}).catch((error) => {
  // 其中一个promise失败了都会进这个方法
}).finally(() => {
  // do something
})

// 等价于
Promise.props({
  promiseResult1 : promise1,
  promiseResult2 : promise2,
}).then(({ promiseResult1, promiseResult2 }) => {})
```



### 串行（不建议）

```js
// 方法2依赖于方法1的结果
function promis1() {
  return new Promise((resolve, reject) => {
    setTimeout(()=>{
        resolve(1)
    },100)}),
  })
}

function promis2() {
  return new Promise((resolve, reject) => {
    setTimeout(()=>{
        resolve(2)
    },200)}),
  })
}

// 慎用
promis1()
  .then((res1) => return promis2())
  .then((res2) => {
    // do somthing 
  })
  .catch((error) => {
    // error
  })

```

参考：[lodash文档](https://www.lodashjs.com/docs/latest)

