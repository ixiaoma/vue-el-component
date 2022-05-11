<template>
    <el-input 
      v-model="inputVal" 
      @keyup.native="keyupEvent(inputVal)" 
      @focus="focusEvent"
      @blur="blurEvent"></el-input>
</template>
<script>
  export default{
    name: 'TrustInputNumber',
    props: {
      value: {
        type: Number,
        default: 0
      }
    },
    data(){
      return {
        inputVal: '0.00',
        num: 0
      }
    },
    watch: {
      value: {
        handler(val){
          if(val == this.num) return
          this.num = val
          this.keyupEvent(val.toString())
        },
        immediate: true
      }
    },
    methods:{
      keyupEvent(numVal){
        let val = numVal;
        val = val
        .replace(/[^\d.]/g, '') // 清除“数字”和“.”以外的字符
        .replace(/\.{2,}/g, '.') // 只保留第一个. 清除多余的
        .replace(/^\./g, '') // 保证第一个为数字而不是.
        .replace('.', '$#$')
        .replace(/\./g, '')
        .replace('$#$', '.')
        if (val.indexOf('.') < 0 && val !== '') {
          // 以上已经过滤，此处控制的是如果没有小数点，首位不能为类似于 01、02的金额
          val = parseFloat(val) + ''
        } else if (val.indexOf('.') >= 0) {
          val = val
            .replace(/^()*(\d+)\.(\d\d).*$/, '$1$2.$3') // 只能输入两个小数
        }
        this.num = val ? Number(val) : 0
        this.inputVal = val.replace(/(\d{1,3})(?=(\d{3})+(?:$|\.))/g, "$1,")
      },
      focusEvent(){
        if(this.inputVal == '0.00'){
          this.inputVal = ''
        }
      },
      blurEvent(){
        if(!this.inputVal){
          this.inputVal = '0.00'
        }else{
          this.inputVal = this.num.toFixed(2).replace(/(\d{1,3})(?=(\d{3})+(?:$|\.))/g, "$1,")
        }
        this.$emit('input', this.num)
      }
    }
  }
</script>
