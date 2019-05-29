<template>
  <section class="input-group-container">
    <van-cell-group class="input-blocker">
      <section class="blocker-bar" v-for="(value,key,index) in inputObj" :key="index">
        <van-field
          v-if="value.type == 'input'"
          v-model.trim="form[key]"
          :class="value.readonly ? 'disabled' : ''"
          :label="value.label"
          :type="value.inputType"
          :clearable="String(value.clearable) == 'false' ? false : true"
          :readonly="String(value.readonly) == 'true' ? true : false"
          :required="String(value.required) == 'true' ? true : false"
          :autosize="true"
          :placeholder="`请输入${value.label}`"
          :error-message="errMsg[key]"
          @blur="validate(value,key)">
        </van-field>
        <van-cell
          v-if="value.type == 'select'"
          is-link
          :title="value.label"
          :value="form[key].name"
          @click="openActionSheet(value,key)">
        </van-cell>
        <van-field
          v-if="value.type == 'countDown'"
          v-model.trim="form[key]"
          :label="value.label"
          :type="value.inputType"
          :clearable="String(value.clearable) == 'false' ? false : true"
          :readonly="String(value.readonly) == 'true' ? true : false"
          :required="String(value.required) == 'true' ? true : false"
          :autosize="false"
          :placeholder="`请输入${value.label}`"
          :error-message="errMsg[key]"
          @blur="validate(value,key)">
          <van-button
            slot="button"
            size="small"
            type="info"
            :disabled="String(sendCodeBtnDisabled) == 'true' ? true : false || !checkPhone"
            @click="sedCode">
            {{ sendCodeBtnText }}
          </van-button>
        </van-field>
      </section>

      <van-actionsheet v-model="actionVisible" :title="actionsTitle">
        <section
          class="select-item van-hairline--bottom"
          v-for="(item,index) in actions"
          :key="index"
          @click="onSelect(item)">
          {{ item.name }}
        </section>
      </van-actionsheet>
    </van-cell-group>

    <section class="trigger-group" v-if="triggerBtn">
      <van-button size="large" type="info" :square="true" class="trigger-btn" @click="trigger">确 定</van-button>
    </section>
  </section>
</template>

<script>
import { getVerifyCode } from '@/api'
import {
  validatePhone,
  validateEmail,
  validatePositiveInteger,
  validateIdNumber,
  isIncludesWhiteSpace,
  isIncludesDot,
  isLongerThan,
  isShorterThan
} from "@/utils";// https://raw.githack.com/ZhengQingFeng/fed-code-snippets/master/Rexp/index.js
export default {
  name: "input-group-container",
  data() {
    return {
      form: {},
      actionField: "",
      actions: [],
      actionsTitle: "",
      actionVisible: false,
      errMsg: {},
      sendCodeBtnDisabled: false,
      sendCodeBtnText: '发送验证码',
      sendCodeCount: 60
    };
  },
  props: {
    inputObj: {
      type: Object,
      required: true
    },
    labelWidth: {
      type: Number,
      default: 80
    },
    triggerBtn: {
      type: Boolean,
      default: true
    },
    autoCheck: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    checkPhone(){
      return validatePhone(this.form.mobile)
    }
  },
  methods: {
    trigger() {
      Object.entries(this.$props.inputObj).forEach(item => this.commonTest(item[1].validator, item[1].label, item[0]))
      let isValid = Object.values(this.errMsg).every(item => item == '')
      if(isValid){
        // console.log('success')
        this.$emit('success',this.form)
      }else{
        // console.log('fail')
      }
    },
    openActionSheet(value, field) {
      this.actionField = field;
      this.actions = value.options;
      this.actionsTitle = value.label;
      this.actionVisible = true;
    },
    onSelect(item) {
      this.form[this.actionField] = item;
      this.actionVisible = false;
    },
    commonTest(validator,label,key){
      if(this.form[key] == '') this.errMsg[key] = `${label}不能为空`
      else if(validator && validator.noBlank && isIncludesWhiteSpace(this.form[key])) this.errMsg[key] = `${label}不能包含空格`
      else if(validator && validator.noDots && isIncludesDot(this.form[key])) this.errMsg[key] = `${label}不能包含.符号`
      else if(validator && validator.maxLength && isLongerThan(this.form[key],validator.maxLength)) this.errMsg[key] = `${label}长度不能大于${validator.maxLength}个字符`
      else if(validator && validator.minLength && isShorterThan(this.form[key],validator.minLength)) this.errMsg[key] = `${label}长度不能小于${validator.minLength}个字符`
      else validator && this.ruleTest(validator.rule,label,key)
    },
    ruleTest(rule,label,key){
      if(rule){
        switch (rule) {
          case 'email':
            if(!validateEmail(this.form[key])) this.errMsg[key] = `${label}格式不正确`
            else this.errMsg[key] = ''
            break;
          case 'mobile':
            if(!validatePhone(this.form[key])) this.errMsg[key] = `${label}格式不正确`
            else this.errMsg[key] = ''
            break;
          case 'positiveInteger':
            if(!validatePositiveInteger(this.form[key])) this.errMsg[key] = `${label}必须为正整数`
            else this.errMsg[key] = ''
            break;
          default :
            this.errMsg[key] = ''
        }
      }
    },
    validate(value, key) {
      if(value.validator){
        this.commonTest(value.validator,value.label,key)
      }
    },
    sedCode(){
      getVerifyCode(this.form.mobile)
      this.sendCodeBtnDisabled = true
      this.sendCodeBtnText = this.sendCodeCount + 's后重新发送'
      let clock = window.setInterval(() => {//倒计时
        this.sendCodeCount --
        this.sendCodeBtnText = this.sendCodeCount + 's后重新发送'
        if (this.sendCodeCount < 0) {
          window.clearInterval(clock)
          this.sendCodeBtnText = '重新发送验证码'
          this.sendCodeCount = 60
          this.sendCodeBtnDisabled = false
        }
      },1000)
    },
    clearFields(){
      Object.keys(this.errMsg).forEach(key => {
        this.errMsg[key] = ''
      })
    }
  },
  created() {
    Object.entries(this.$props.inputObj).forEach(item => {
      this.$set(this.form, item[0], item[1].default || '');
      this.$set(this.errMsg, item[0], "");
    });
    this.$props.autoCheck && Object.entries(this.$props.inputObj).forEach(item => {
      this.commonTest(item[1].validator, item[1].label, item[0])
    })
  },
  mounted() {
    let els = document.querySelectorAll("div.van-field__label");
    els.forEach(item => (item.style.maxWidth = this.$props.labelWidth + 'px'));
  }
};
</script>

<style lang="scss" scoped>
.input-group-container {
  .input-blocker {
    margin-bottom: 10px;
    .blocker-bar {
      .van-cell:after {
        content: " ";
        position: absolute;
        pointer-events: none;
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
        left: 15px;
        right: 0;
        bottom: 0;
        -webkit-transform: scaleY(0.5);
        transform: scaleY(0.5);
        border-bottom: 1px solid #ebedf0;
      }
    }
    .select-item {
      line-height: 40px;
      text-align: center;
    }
  }
  .trigger-group {
    // padding: 0 10px;
    .trigger-btn {
      margin-bottom: 10px;
      height: 40px;
      line-height: 40px;
      font-size: 14px;
    }
  }
  .disabled{
    /deep/.van-field__body{
      .van-field__control{
        color: #aaa;
      }
    }
  }
}
</style>
