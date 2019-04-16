## zuniSlither@1.0.2 滑动操作

uni-app 组件，适用于h5、小程序，暂不支持app。
后续根据情况补充完善。

### 使用栗子

```js
<template>
<zuni-slither v-for="(data, index) in dataList" :key="index" :actions="actions" :active-index.sync="activeIndex" :fade="true" @click="handleClick">
  <view>{{data.title}}</view>
</zuni-slither>
</template>

<script>
import zuniSlither from '@/components/zuni-slither/zuni-slither'
export default {
  components: {
    zuniSlither
  },
  data(){
    return {
      // 数据列表
      dataList: [{
        title: 'one',
        id: 0
      }, {
        title: 'two',
        id: 1
      }],
      // 索引(设置为null即可)
      activeIndex: null,
      // 行为
      actions: [{
        title: '编辑',
        style: {
          color: 'white',
          background: 'orange'
        },
        trigger(index){
          console.log('edit', index)
        }
      }, {
        title: '删除',
        style: {
          color: 'white',
          background: 'red'
        },
        trigger(index){
          console.log('delete', index)
        }
      }]
    }
  },
  methods: {
    handleClick({event, activeStatus, index}){
      console.log(event, activeStatus, index)
    }
  }
}
</script>
```

### 属性

| 参数             | 说明       | 类型         | 必填 | 可选值 | 默认值 |
| ---------------- | ---------- | ------------ | ---- | ------ | ------ |
| actions          | 操作行为   | Array        | 是   | ——   | 见下方 |
| activeIndex.sync | 滑动项索引 | Number, null | 是   | ——   | null   |
| fade         | 操作栏折叠时透明   | Boolean      | 否   | ——   | false  |
| disabled         | 禁止滑动   | Boolean      | 否   | ——   | false  |

```js
// actions 默认值
actions = [{
  // 文本显示
  title: '操作',
  // 操作样式
  style: {
    width: 180, // 不带单位哦
    color: '#fff',
    background: '#D65649'
  },
  // 事件
  trigger(index){
    console.log(index)
  }
}]
```

### 事件

| 事件名称 | 说明     | 回调参数              |
| -------- | -------- | --------------------- |
| click    | 点击事件 | {event, activeStatus, index} |
| slide-out    | 操作行为滑出事件 | index |

#### event 为事件对象，activeStatus 为是否存在滑块滑出, index 为当前点击项索引

## change log

### 2019.04.16 @1.2.0

- 添加属性 `fade` ，默认为 `false`。在滑动列表与页面有间距时可设置为 `true` 隐藏操作行为。

- 添加操作行为滑出回调方法 `slide-out`

### 2019.04.15 @1.0.2 
 
 - 修复click事件回调参数 `activeStatus` 不正确问题。
 
### 2019.04.14 @1.0.1 

- 添加click事件回调参数 `index` 字段。