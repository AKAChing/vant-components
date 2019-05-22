# vant 日期范围选择组件
## 此组件有两种模式, 一种快捷选择, 一种手动选择范围, 详情见demo

> 引用并注册组件

``` javascript
  import { DateRangeSelector } from './index.vue'
```

> 手动选择 [在线Demo](https://codepen.io/zhengqingfeng/pen/ZNazqo)

``` html
  <van-button @click="open">打开组件</van-button>
  <date-range-selector
    ref="dateRangeSelector"
    selector-type="shortcut"
    @get-date-range="getFilterData">
  </date-range-selector>
```

> 快捷选择 [在线Demo](https://codepen.io/zhengqingfeng/pen/wbPQbj)

``` html
  <van-button @click="open">打开组件</van-button>
  <date-range-selector
    ref="dateRangeSelector"
    selector-type="range"
    @get-date-range="getFilterData">
  </date-range-selector>
```

``` javascript
  open(){
    this.$refs.dateRangeSelector.open()
  }

  getFilterData(time){
    console.log(time)
    // ["2019-5-21", "2019-5-21"]
  }
```

> 参数

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
| :--: | :--: | :--: | :--: | :--: |
| selector-type | 组件类型 | string | 'range' | 'range' / 'shortcut' |

> 事件

| 方法名 | 说明 | 参数 |
| :--: | :--: | :--: |
| get-date-range | 返回日期范围 | dateObj |