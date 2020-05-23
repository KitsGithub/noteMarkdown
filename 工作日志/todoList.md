## TODO list 

### Trident

- [ ] 走查 **Fg-avatar** 的点击事件与实际有差异
- [ ] 设置服务城市后，本地数据没有被覆盖掉
- [ ] 除了关联企业不改，在企业号页面，其他“企业”都改成“企业号”
- [x] 首页-请求未读消息接口逻辑变更，与UI逻辑分离。
- [ ] app级别的缓存池，处理页面级别和组件级别之前的通讯问题
- [x] 原生改动点：longinUser里的department结构新增 `hasSubGroup` *Int* 类型
- [x] 新增原生底部Tab，放在首页和客户之间。默认加载weex页面`manager`
- [ ] 点击消息推送，跳转到消息页
- [ ] Andriod关键人红点显示缺失



## 雨水迭代遗留问题
- [ ] 折线图里的图例数字还是宋体
- [ ] 首页-常用-附近经纪人换行了
- [ ] 饼图内的数字都需要带上单位
- [ ] 客户类型顺序没换
- [ ] 商机分析-折线图-线段颜色-对不上图例
- [ ] 折线图未来时间不显示







安卓原生改动

```js
1. 新增桥方法`wxworkShare`
参数 param: {
  type: String 取值范围：TEXT, LINK, FILE, LOCATION
  title, String 分享的标题
  url, String 分享的链接
  summary, Sring 分享的内容
}
链接分享缩略图固定为一个：https://lanhuapp.com/web/#/item/project/board/detail?pid=82b59821-4974-44e5-891a-5300589d3480&project_id=82b59821-4974-44e5-891a-5300589d3480&image_id=e36f8680-6aa2-4a5c-b6e1-062165ac742e
```



- 新增桥方法`wxworkShareLink`
  - 参数 - `param` 

- 通讯录新增 `*hasSubGroup*`字段

- 颜色配置变更

  ```js
  "#a0a7e6",
  "#5196e5",
  "#6be6c1",
  "#4f7ba4",
  "#cbb0e3",
  "#96dee8"
  ```

  




### 多余页面

- deptstatistics 团队管理
- membercheckin 成员所有签到页
- home.vue 旧首页

