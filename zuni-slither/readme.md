## zuniSlither@1.0.0 滑动操作

uni-app 组件，适用于h5、小程序，暂不支持app。
后续根据情况补充完善。

如有问题，可加qq:921674302反馈咨询。

### 使用栗子

```js
<template>
<zuni-slither v-for="(data, index) in dataList" :key="index" :actions="actions" :active-index.sync="activeIndex" @click="handleClick">
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
        trigger(){
          console.log('edit')
        }
      }, {
        title: '删除',
        style: {
          color: 'white',
          background: 'red'
        },
        trigger(){
          console.log('delete')
        }
      }]
    }
  },
  methods: {
    handleClick({event, activeStatus}){
      console.log(event, activeStatus)
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
  trigger(){
    console.log('trigger')
  }
}]
```

### 事件

| 事件名称 | 说明     | 回调参数              |
| -------- | -------- | --------------------- |
| click    | 点击事件 | {event, activeStatus} |

#### event 为事件对象，activeStatus 为是否存在滑块滑出