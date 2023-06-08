<template>
  <el-form ref="loginForm" :model="form" :rules="rules" :inline="true">
    <el-form-item label="手机号" prop="phone">
      <el-input type="text" maxlength="11" placeholder="手机号" style="width: 250px" v-model="form.phone" />
    </el-form-item>
    <el-form-item label="验证码" prop="code">
      <el-input type="text" maxlength="6" placeholder="验证码" v-model="form.code" style="width: 250px" />
      <el-button :disabled="disabled" @click="getCode">{{ valiBtn }}</el-button>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click.prevent="onSubmit('loginForm')">登 录</el-button>
    </el-form-item>
  </el-form>
</template>
<script>
export default {
  data() {
    var checkPhone = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入绑定的手机号码"))
      } else if (!/^(13[0-9]|14[5|7]|15[0|1|2|3|4|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$/.test(value)) {
        callback(new Error("请输入正确的手机号码"))
      } else {
        callback()
      }
    };
    var checkCode = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入验证码"))
      } else if (!/^[0-9]+$/.test(value) || !/^\d{6}$/.test(value)) {
        callback(new Error("请输入6位数字的验证码"))
      } else {
        callback()
      }
    };
    return {
      form: {
        phone: '',
        code: ''
      },
      autoLogin: false,
      rules: {
        phone: [{ validator: checkPhone, required: true, trigger: 'blur' }],
        code: [{ validator: checkCode, required: true, trigger: 'blur' }]
      },
      valiBtn: '获取验证码',
      disabled: false,
    }
  },
  methods: {
    //获取验证码 并只验证手机号 是否正确
    getCode() {
      this.$refs['loginForm'].validateField('phone', (err) => {
        if (err) {
          console.log('未通过')
          return;
        } else {
          console.log('已通过')
          console.log(this.form.phone)
          this.tackBtn();   //验证码倒数60秒
        }
      })
    },
    tackBtn() {       //验证码倒数60秒
      let time = 60;
      let timer = setInterval(() => {
        if (time == 0) {
          clearInterval(timer);
          this.valiBtn = '获取验证码';
          this.disabled = false;
        } else {
          this.disabled = true;
          this.valiBtn = time + '秒后重试';
          time--;
        }
      }, 1000);
    },
    onSubmit(formName) {  //点击登录 验证手机& 验证码是否符合条件
      this.$refs[formName].validate((valid) => {    // 为表单绑定验证功能
        if (valid) {
          this.$alert("成功", "提示")
        }
      });
    },
  },
}
</script>
