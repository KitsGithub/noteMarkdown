## Fg-form

- 日志
  - 文档构建
- TODO
  - 动态增减表单


### 底层基础组件

#### fg-form-item

- 基础组件，提供行内验证表单内容

| 参数           |                        说明                         |   类型    |        可选值        | 默认值  |
| :------------- | :-------------------------------------------------: | :-------: | :------------------: | :-----: |
| props          |                   表单绑定的对象                    |           |                      |    -    |
| label          |                      展示文本                       | `String`  |          -           |    -    |
| rules          |                       `Rule`                        | `Object`  |          -           |    -    |
| show-message   |                  是否展示错误信息                   | `boolean` |          -           | `true`  |
| error          | 表单验证的错误信息，设置该值会使该表单验证为`error` | `String`  |          -           |    -    |
| label-width    |           表单域标签的的宽度，例如 '50wx'           | `String`  |                      |         |
| label-position |                    表单对齐方式                     | `String`  | `TOP`/`LEFT`/`RIGHT` | `LEFT`  |


```js
Rule: {
  required: true, // 是否为必填字段
  validator: (value, resole, reject) => {
    if (value.length) {
      resole()
    } else {
      reject(new Error('请输入内容'))
    }
  }
}
```

##### 方法

###### reset

- 重置该表单

```js
this.$ref.form.reset()
```

###### validate

- 校验该表单

```js
this.$ref.form.validate()
```



### 上层封装组件

#### fg-form-selected

- 点击后会回调的表单

#### fg-form-checked

- 提供单选、多选能力的表单

#### fg-form-image-picker

- 提供从相册选择图片的能力

#### fg-form-customer

- 提供自定义内容插槽的表单

#### fg-form-Input

- 提供单行文本输入框，可限制数字、文字长度等

#### fg-form-textArea

- 提供多行文本输入框

#### fg-form-dynamic

- 提供动态数量的表单项





## Fg-form

- 简述： 通过config来配置一个表单页

### API

| Props            | 说明               | 类型     | 默认值 | 版本 |
| ---------------- | ------------------ | -------- | ------ | ---- |
| `form`           | 表单参数及规则配置 | `Object` | -      | -    |
| `label-position` | 表单对齐方式       | `String` | `LEFT` | -    |

### 方法

#### validate

- 描述： 提供表单验证方法

```js
this.$ref.form.validate()
	.then((valid) => {
  	// do something ...
})
```

#### resetFrom

- 描述：重置表单

```js
this.$ref.form.resetForm()
```



### 数据结构

```js
form = [ FORM ]

FORM: {
  required: Boolean, // 是否为必填字段
  title: String,	// 标题
  value: Object, // 可以是字符串，数字，数组，Object
  formType: FORM_TYPE,
	rules: String, // 正则表达 （如特殊的字数限制）
  // value的展示规则，如value = 0 的时候展示“门店”
  formatter: function formatter(value) => { return '' }, 
  children: [ FORMTAG ], // 仅在 CHECK_BOX 下生效
  disable: Boolean, // 是否可输入
  config: {
    customerType: 'AAA', // 业务方定制cell的样式标识
  },
  errorMessage: '请填写正确的培训人数'
}


// FORM_TYPE 取值范围
FORM_TYPE = {
 	'CHECK_BOX': 选择类型
  - 'CHECK_BOX_SINGLE': 单选
  - 'CHECK_BOX_MULITI': 多选
  'SELECT': 点击类型
  'INPUT': 无规则输入框
   - 'INPUT_TEXT': 限制字符串输入框
   - 'INPUT_NUMBER': 限制数字输入框
   - 'INPUT_TEXTAREA': 多行输入框
  'CUSTOMER': 自定义类型,需自行实现cell的样式
  'IMAGE': 图片选择
}

// TAG的数据结构
FORMTAG = {
  name: String,
  key: String/Number
}

// label-position 表单对齐方式
label-position = {
  'TOP':	 顶部对齐，title会放在表单顶部
  'LEFT':  左对齐
  'RIGHT': 右对齐
}

```

```vue
// 使用方法
<template>
	<Fg-form  :from="formData"/>
</template>

<script>
  data() {
    return {
      formData : {
        name: '培训对象',
        formType: 'CHECK_BOX',
        isKey: true,
        
      }
    }
  }
</script>


```

