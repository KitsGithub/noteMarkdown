## Weex的发展

### 目前Weex的现状问题

1. 对跨页面数据传递的复杂度高
2. 缺乏原生组件支持
3. 基础组件太少
4. 缺少交互，体验不好
5. 使用成本大、复杂度高
6. 没有形成统一的代码规范
7. 桥方法的泛滥、定义不准确

#### 对跨页面数据传递的复杂度高

对weex之间、weex与原生、原生与weex之间的数据传递复杂

往往需要这么去定义

```js
// weex To weex
// A页面（传递方）
this.openWeexUrl(`manager?type=DEPARTMENT&department=${encodeURI(JSON.stringify(target))}&date=${this.selectorQuery.date}`)

// B页面（接收方）
const departmentJsonStr = utils.getQueryString('department')
try {
  this.department = JSON.parse(decodeURI(departmentJsonStr))
  this.selectorQuery.department = this.department.id
} catch (error) {
  this.department = {}
  this.selectorQuery.department = ''
}
```



#### 缺乏原生组件支持

包括但不仅限于一下几点

1. 如多个同向列表组合的详情页
2. 缺少非阻塞loadding
3. 键盘事件的处理

#### 缺少交互，体验不好

1. 缺乏对weex提供的animation模块的探索
2. 缺乏对[BindingX](https://gitee.com/Carmi/BindingX)的探索
3. 对自定义动画的探索

当前迭代已经开始对animation的探索了

- 在首页看到添加了一点简单的折叠动画
- 在首页-门店签到-点击门店-门店详情-section与内容区的联动展示
- 门店详情-点击关键人-弹出关键人的名词解释

可以看到这个还是比较接近原生的效果的，之前不做确实是缺少探索与尝试

#### 没有统一的规范

1. 样式的统一
2. 组件的统一
3. 代码规范

#### 使用成本大、复杂度高

> 成本大体现在没有统一规范下的成本大

1. 各个相似组件之间的选择
2. 数据结构的包装、改造等等
3. 没有统一的传参、入参格式

```js
如：在定义page的传参形式，项目中有2种
// 形式一：
props: {
  date: {
    type: Number,
    default: new Date().getTime(),
  },
}

// 形式二
data() {
    return {
      date: utils.getQueryString('id') || '',
    }
}
```

#### 桥方法的泛滥、定义不准确



### Weex的发展方向

#### 短期目标

1. 加速基础组件的建设

   表单组件的完善、本地存储功能的完善、对组件生命周期的完善、对多列表页的底层建设、对原生基础组件的对接

2. 加速推进BC的落地

   将已完成的部分基建项，推广到BC并落地相关用法与规范

3. 配合BC原生基建项，形成原生+Weex相互配合的循环

4. Weex基建库demo

#### 长期目标

1. 持续推进weex在BCT的落地与维护

2. 产品的演进慢慢从原生向大前端发展

3. 逐渐形成类似于`Element`的框架

4. 降低使用和学习成本。让任何人都能快速上手、减少人为错误

5. weex的独立，单独成项，快速搭建项目

   

### 各端的配合工作

1. 人员的配备
2. 各端Weex需求的协作与规范
3. 配合推进基建项的讨论与开发
4. 推广weex基建项



### 个人思考

#### 怎么看待weex

对weex而言，是一个已经停止维护有一段时间的一个框架，有一定的滞后性。往往跟不上系统平台的迭代，但是为什么还要继续探索？我觉得基于以下几点：

1. 但是如果吧上面所有关于Weex的字眼全部换成`大前端`其实就不会被weex的框架所限制。我们的基建项也能无损迁移（只要大前端语言能够使用`vue`的框架）
2. 大前端的特性能够适应产品快速迭代、小步快走的策略，更快的响应市场的情况。
3. 相对于原生，前端的稳定性会更好（这里的稳定性从报错情况、崩溃表现来评判），但是前端的报错相对隐秘。