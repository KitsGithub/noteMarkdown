## fg-base-component
*原生基础组件*

### 生命周期方法

#### onCompomentLoadFinished

*组件初始化完成回调。变更组件内容或调用组件方法需要在这个方法实现*

```js
<template>
  <fg-web-view ref="web" @onCompomentLoadFinished="onCompomentLoadFinished"/>
</template>

<script>
  //...
  methods: {
    onCompomentLoadFinished() {
			// do something
      // eg. this.$ref.setContent('XX')
    }
  }
</script>
```

