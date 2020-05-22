

## Lodash 的使用

### 高频使用方法

### `_.get(object, path, [defaultValue])`

```javascript
/*
result = {
	result: [
		{
			"text": String,
			"isHot": Boolean,
		}
	], // 默认是空数组
	nextId: "1", // 如果没有下次请求，则无nextId字段
}
query = {
	keyword: '',
	nextId: '',
}
*/
servce.requireNetwork(query)
	.then((result) => {
    // 一般写法
  	let nextId = result.nextId
    // nextId 的值可能是: undefind, "1"
    
    // lodash写法
    let nextId = _.get(result, "nextId", "")
    // nextId 的值可能是: "", "1"
	})
```



### `_.assign(object, [sources])`

> 来源对象的应用规则是从左到右，随后的下一个对象的属性会覆盖上一个对象的属性。

```vue
<template>
  <div>
    <button title="排序" @click="onSort" />
    <div v-for="(object, index)" in dataSourceList :key="index">
      <text>{{ object.text }}</text>
    </div>
  </div>
<template>

<script>
servce.requireNetwork(query)
  .then((result) => {
    let resultList = _.get(result, "result", [])
   	for (let i = 0; i < resultList.length; i++) {
      let object = resultList[i]
      // 一般写法
      object.text = `**${object.text}**`
      // lodash写法1
      _.assign(object, { text: `**${object.text}**` }) 
      // lodash写法2
      object = _.assign({}, object, { text: `**${object.text}**` })
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
    
		this.dataSourceList = resultList
	})
</script>
```



### `_.filter(collection, [predicate=_.identity])`

```vue
<script>
servce.requireNetwork(query)
  .then((result) => {
    let resultList = _.map(_.get(result, "result", []), (object) => {
      return _.assign({}, object, { text: `**${object.text}**` })
    })
    
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
	})
</script>
```





- 复杂操作（链式）

```

```

