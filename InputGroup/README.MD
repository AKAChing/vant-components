# 表单交互组件 [Demo](https://codepen.io/zhengqingfeng/pen/joYPrW)

> 引用并注册组件

``` javascript
  import { InputGroup } from './index.vue'
```

``` html
  <input-group :input-obj="inputObj" @success="valSuccess" ref="inputGroup"></input-group>
```

> 组件内置了部分验证规则, 邮箱手机号是否合法, 是否不能为空, 最大长度/最小长度/长度是否合法, 是否包含小数点...

``` javascript
  let inputObj = {
    username: {
      type: 'input',
      inputType: 'text',
      label: '电子邮箱',
      default: '287758560@qq.com',
      required: true,
      validator: {
        rule: 'email',
        noBlank: true,
        noDots: false,
        maxLength: 20,
      }
    },
    password: {
      type: 'input',
      inputType: 'password',
      label: '密码',
      default: '123456',
      required: true,
      validator: {
        rule: 'common',
        noBlank: true,
        noDots: false,
        maxLength: 12,
        minLength: 6,
      }
    },
    mobile: {
      type: 'input',
      inputType: 'tel',
      label: '手机',
      default: '17306024809',
      required: true,
      validator: {
        rule: 'mobile',
        noBlank: true,
        noDots: true,
        maxLength: 11,
      }
    },
    age: {
      type: 'input',
      inputType: 'tel',
      label: '年龄',
      default: '18',
      required: true,
      validator: {
        rule: 'positiveInteger',
        noBlank: true,
        noDots: true,
        maxLength: 2,
      }
    },
    sex: {
      type: 'select',
      label: '性别',
      options: [{name: '男',key: 1},{name: '女',key: 2}],
      default: {name: '男',key: 1}
    },
    des: {
      type: 'input',
      inputType: 'textarea',
      label: '简介',
      clearable: false,
      default: '介绍信息介绍信息介绍信息介绍信息介绍信息介绍信息...',
    },
    code: {
      type: 'countDown',
      inputType: 'number',
      label: '验证码',
      default: '1111',
      required: true,
      validator: {
        rule: 'positiveInteger',
        noBlank: true,
        noDots: true,
        maxLength: 4,
        minLength: 4,
      }
    },
  }

  valSuccess(obj){
    console.log(obj)
    // {
    //   age: "18",
    //   code: "1111",
    //   des: "介绍信息介绍信息介绍信息介绍信息介绍信息介绍信息...",
    //   level: {
    //     key: 3,
    //     name: "3"
    //   },
    //   mobile: "17306024809",
    //   password: "1234456",
    //   sex: {
    //     key: 1,
    //     name: "男"
    //   },
    //   username: "287758560@qq.com"
    // }
  }
```

> 组件参数

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
| :--: | :--: | :--: | :--: |  :--: |
| input-obj | 表单对象 | object | - |  - |
| label-width | label宽度 | number | 80 |  - |
| triggerBtn | 是否显示确认按钮 | boolean | true |  - |
| autoCheck | 是否开启初始化检测 | boolean | false |  - |

> 表单对象参数

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
| :--: | :--: | :--: | :--: |  :--: |
| input-obj[key].type | 对应项的表单类型 | string | - | input/select/countDown |
| input-obj[key].inputType | 对应项的实体交互表单类型 | string | - | text/password/tel/textarea/number |
| input-obj[key].label | 对应项的label | string | - | - |
| input-obj[key].default | 对应项的默认值 | string | - | - |
| input-obj[key].required | 对应项是否必填 | boolean | false | - |
| input-obj[key].readonly | 对应项是否只读 | boolean | false | - |
| input-obj[key].clearable | 对应项是否有清除按钮 | boolean | true | - |

> 验证器参数

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
| :--: | :--: | :--: | :--: |  :--: |
| input-obj[key].validator | 对应项输入校验 | object | - |  - |
| input-obj[key].validator.rule | 对应项输入校验规则 | string | - |  common/email/mobile/positiveInteger |
| input-obj[key].validator.noBlank | 对应项输入校验规则-是否包含空格 | boolean | false | - |
| input-obj[key].validator.noDots | 对应项输入校验规则-是否包含小数点 | boolean | false | - |
| input-obj[key].validator.maxLength | 对应项输入校验规则-最大长度 | boolean | false | - |
| input-obj[key].validator.minLength | 对应项输入校验规则-最小长度 | boolean | false | - |


> 事件

| 方法名 | 说明 | 参数 |
| :--: | :--: | :--: |
| success | 返回表单数据 | obj |


> 可以通过refs获取目标然后调用内部事件
- this.$refs.inputGroup.clearFields() 清除表单对象的校验信息
- this.$refs.inputGroup.trigger() 当不需要内部的确定按钮, 自己另外写一个按钮绑定一个事件并调用此方法达到success一样的效果