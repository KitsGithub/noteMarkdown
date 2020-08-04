

## WeexDataCache

### 背景

- 当Weex需要获取类似于`全局配置`这样的一个大数据量的子集的时候，会出现性能上的瓶颈，往往需要加载很久
- 对于上述问题，Realm 没法设置主键以达到优化查询的目的
- 不同页面间的数据传递复杂无法联动（目前是通过Boardcost来传递数据）
- Weex页面与原生页面间数据传递困难

基于上述几点，在原生与weex间，weex与weex间的数据传递需要以更高效的形式进行传递

### 目的

- 主要是为了优化在数据传输方面的速度与效率
- 解决weex与weex间只能传string类型的问题
- 解决weex与weex间传参暴露到url的问题

### 统计方法

- 横向对比大数据量与小数据量

- 纵向对比大数据量中，取小数据量的属性与大数据量的属性

- 数据量大小计算方法如下

  ```js
  function getLength(val) {  
      var str = new String(val);  
      var bytesCount = 0;  
      for (var i = 0 ,n = str.length; i < n; i++) {  
          var c = str.charCodeAt(i);  
          if ((c >= 0x0001 && c <= 0x007e) || (0xff60<=c && c<=0xff9f)) {  
              bytesCount += 1;  
          } else {  
              bytesCount += 2;  
          }  
      }  
      return bytesCount;  
  }
  ```

- 采取串行的方式，循环调用该桥方法100次，实验5组，取前后时间差值的中位数

  ```js
  // BridgeMethod 为不同的桥方法
  let count = 0
  let newPromise = () => {
    return this.callNative('BridgeMethod').then((res) => {
      if (count < 100) {
        count ++
        return newPromise()
      }
      return res
    })
  }
  
  console.log('startTime = ', new Date().getTime())
  newPromise().then((res) => {
    console.log('endTime = ', new Date().getTime())
  })
  ```

  

### 统计结果

#### 横向对比（小数据量与大数据量耗时对比）

##### 小数据量

> 以`getLoginUserInfo`为例，该方法用于获取当前登录用户的信息，如头像、名字、token等
>
> 数据样本为876个字节

结果

|          | 第一次 | 第二次 | 第三次 | 第四次 | 第五次 |
| -------- | ---- | ---- | ---- | ---- | ------ |
| 开始时间 | 1590656877160 | 1590657221963 | 1590657332168 | 1590657370415 | 1590657997859 |
| 结束时间 | 1590656878073 | 1590657222888 | 1590657333080 | 1590657371291 | 1590657998737 |
| 差值     | `883ms` | `925ms` | `912ms` | `876ms` | `878ms` |
| 中位数   |      |      |      |      | `894.8ms` |

##### 大数据量

> 以`getClientConfig`为例，该方法用于获取下发的配置项

结果

|          | 第一次        | 第二次  | 第三次  | 第四次  | 第五次  |
| -------- | ------------- | ------- | ------- | ------- | ------- |
| 开始时间 | 1590654673585 |         |         |         |         |
| 结束时间 | 1590654729263 |         |         |         |         |
| 差值     | 55678≈55.6s   | `925ms` | `912ms` | `876ms` | `878ms` |
| 中位数   |               |         |         |         |         |



#### 纵向对比（大数据量中的小数据量属性与大数据量属性）

##### 小数据量

> 以全局配置中的`CHECKIN_MAX_DISTANCE`为例

结果

|          | 第一次        | 第二次  | 第三次  | 第四次  | 第五次  |
| -------- | ------------- | ------- | ------- | ------- | ------- |
| 开始时间 | 1590655073175 |         |         |         |         |
| 结束时间 | 1590655076767 |         |         |         |         |
| 差值     | 3592 ≈ 3.5s   | `925ms` | `912ms` | `876ms` | `878ms` |
| 中位数   |               |         |         |         |         |



##### 大数据量

> 以全局配置中的`CHECKIN`为例

结果

|          | 第一次        | 第二次 | 第三次 | 第四次 | 第五次 |
| -------- | ------------- | ------ | ------ | ------ | ------ |
| 开始时间 | 1590654673585 |        |        |        |        |
| 结束时间 | 1590654729263 |        |        |        |        |
| 差值     | 55678≈55.6s   |        |        |        |        |
| 中位数   |               |        |        |        |        |
