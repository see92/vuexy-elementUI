<template>
  <div class="auth-wrapper auth-v2">
    <b-row class="auth-inner m-0">

      <!-- Brand logo-->
      <b-link class="brand-logo">
        <vuexy-logo />
        <h2 class="brand-text text-primary ml-1">
          Vuexy
        </h2>
      </b-link>
      <!-- /Brand logo-->

      <!-- Left Text-->
      <b-col lg="8" class="d-none d-lg-flex align-items-center p-5">
        <div class="w-100 d-lg-flex align-items-center justify-content-center px-5">
          <b-img fluid :src="imgUrl" alt="Login V2" />
        </div>
      </b-col>
      <!-- /Left Text-->

      <!-- Login-->
      <b-col lg="4" class="d-flex align-items-center auth-bg px-2 p-lg-5">
        <b-col sm="8" md="6" lg="12" class="px-xl-2 mx-auto">
          <b-card-title title-tag="h2" class="font-weight-bold mb-1">
            Welcome to Vuexy! 👋
          </b-card-title>
          <b-card-text class="mb-2">
            Please sign-in to your account and start the adventure
          </b-card-text>

          <el-form ref="loginForm" :model="form" label-position="right" label-width="100px" :rules="rules">
            <el-form-item label="手机号码：" prop="phone">
              <el-input v-model="form.phone" />
            </el-form-item>
            <el-form-item label="验证码：" prop="code">
              <div class="d-flex">
                <el-input v-model="form.code" />
                <div class="ml-1">
                  <el-button :disabled="disabled" @click="getCode" style="width:84px">{{ msg }}</el-button>
                </div>
              </div>
            </el-form-item>
            <div>
              <el-button style="width:100%" type="primary">登录</el-button>
            </div>
          </el-form>

          <b-card-text class="text-center mt-2">
            <span>New on our platform? </span>
            <span>&nbsp;Create an account</span>
          </b-card-text>

          <!-- divider -->
          <div class="divider my-2">
            <div class="divider-text">
              or
            </div>
          </div>

          <!-- social buttons -->
          <div class="auth-footer-btn d-flex justify-content-center">
            <b-button variant="facebook" href="javascript:void(0)">
              <feather-icon icon="FacebookIcon" />
            </b-button>
            <b-button variant="twitter" href="javascript:void(0)">
              <feather-icon icon="TwitterIcon" />
            </b-button>
            <b-button variant="google" href="javascript:void(0)">
              <feather-icon icon="MailIcon" />
            </b-button>
            <b-button variant="github" href="javascript:void(0)">
              <feather-icon icon="GithubIcon" />
            </b-button>
          </div>
        </b-col>
      </b-col>
      <!-- /Login-->
    </b-row>
  </div>
</template>

<script>
/* eslint-disable global-require */
import { ValidationProvider, ValidationObserver } from 'vee-validate'
import VuexyLogo from '@core/layouts/components/Logo.vue'
import {
  BRow, BCol, BLink, BFormGroup, BFormInput, BInputGroupAppend, BInputGroup, BFormCheckbox, BCardText, BCardTitle, BImg, BForm, BButton,
} from 'bootstrap-vue'
import { required, email } from '@validations'
import { togglePasswordVisibility } from '@core/mixins/ui/forms'
import store from '@/store/index'
import ToastificationContent from '@core/components/toastification/ToastificationContent.vue'

export default {
  components: {
    BRow,
    BCol,
    BLink,
    BFormGroup,
    BFormInput,
    BInputGroupAppend,
    BInputGroup,
    BFormCheckbox,
    BCardText,
    BCardTitle,
    BImg,
    BForm,
    BButton,
    VuexyLogo,
    ValidationProvider,
    ValidationObserver,
  },
  mixins: [togglePasswordVisibility],
  data() {
    var checkPhone = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入绑定的手机号码"))
      } else if (!/^(13[0-9]|14[5|7]|15[0|1|2|3|4|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$/.test(value)) {
        callback(new Error("请输入正确的手机号"))
      } else {
        callback()
      }
    };
    var checkCode = (rule, value, callback) => {
      if (value == '') {
        callback(new Error("请输入验证码"))
      } else if (!/^[0-9]+$/.test(value) || !/^\d{6}$/.test(value)) {
        callback(new Error("请输入6位数字的验证码"))
      } else {
        callback()
      }
    }
    return {
      status: '',
      password: '',
      userEmail: '',
      sideImg: require('@/assets/images/pages/login-v2.svg'),
      form: {
        phone: '',
        code: ''
      },
      rules: {
        phone: [{ validator: checkPhone, required: true, trigger: 'blur' }],
        code: [{ validator: checkCode, required: true, trigger: 'blur' }]
      },
      msg: '验证码',
      disabled: false
    }
  },
  computed: {
    imgUrl() {
      if (store.state.appConfig.layout.skin === 'dark') {
        // eslint-disable-next-line vue/no-side-effects-in-computed-properties
        this.sideImg = require('@/assets/images/pages/login-v2-dark.svg')
        return this.sideImg
      }
      return this.sideImg
    },
  },
  methods: {
    getCode() {
      this.$refs['loginForm'].validateField('phone', (err) => {
        if (err) {
          console.log('未通过');
          return
        } else {
          console.log('已通过');
          console.log(this.form.phone, 'phone');
          this.countdown()
        }
      })
    },
    countdown() {
      let time = 60;
      let timer = setInterval(() => {
        if (time == 0) {
          clearInterval(timer);
          this.msg = '验证码'
          this.disabled = false
        } else {
          this.disabled = true
          this.msg = time + '秒'
          time--;
        }
      }, 1000)
    }
  },
}
</script>

<style lang="scss">
@import "@core/scss/vue/pages/page-auth.scss";
</style>
