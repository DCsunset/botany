<template>
  <div>
    <el-row v-loading="loading" :gutter="20">
      <el-col :span="12">
        <el-card shadow="none" style="margin-top: 180px; border: none; height: 280px">
          <div><img :src="require('../assets/logo.png').default" style="height: 280px"/></div>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card shadow="always" class="login-container" style="margin-top: 180px; border: none">
          <div slot="header">
            <div class="login-header">登录BotAny</div>
          </div>
          <div class="login-main">
            请从THU AI主网站登录本赛道
          </div>
          <el-button style="width: 60%; margin-top: 40px" @click="goToMain">
            前往主网站
          </el-button>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'login',
  created () {
    if (this.$route.query && this.$route.query.redirect && this.$store.state.afterLogin) {
      this.nextRoute = this.$store.state.afterLogin
      this.redirect = this.$route.query.redirect
    }
  },
  data () {
    return {
      loading: false,
      loginInfo: {
        handle: '',
        password: ''
        // captcha: ''
      },
      captcha64: '',
      captchaKey: '',
      loginErrHandle: '',
      loginErrPswd: '',
      loginErrCpch: '',
      nextRoute: {
        path: '/'
      },
      redirect: false,
      rules: {
        handle: [
          { required: true, message: '请输入账号', trigger: 'blur' },
          { max: 30, message: '输入过长', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { max: 30, message: '输入过长', trigger: 'blur' }
        ]
        // captcha: [
        //   {required: true, message: '请输入验证码', trigger: 'blur'},
        //   {min: 4, max: 4, message: '请输入4个字符', trigger: 'blur'}
        // ]
      }
    }
  },
  methods: {
    getCaptcha () {
      this.$axios.get(
        '/captcha'
      ).then(res => {
        this.captcha64 = res.data.img
        this.captchaKey = res.data.key
      // eslint-disable-next-line handle-callback-err
      }).catch(err => {
        this.$message.error('无法获取验证码，请检查网络')
      })
    },
    login () {
      this.$refs.loginform.validate(valid => {
        if (valid) {
          this.loginErrHandle = ''
          this.loginErrPswd = ''
          this.loginErrCpch = ''
          this.loading = true
          const params = this.$qs.stringify({
            handle: this.loginInfo.handle,
            password: this.loginInfo.password
          })
          this.$axios.post(
            '/login',
            params
          ).then(res => {
            const logindata = {
              id: res.data.id,
              handle: res.data.handle,
              privilege: parseInt(res.data.privilege),
              nickname: res.data.nickname
            }
            this.$store.commit('login', logindata)
            this.loading = false
            console.log(this.nextRoute)
            this.$router.push(this.nextRoute)
          }).catch(err => {
            this.loading = false
            console.log(err)
            this.$message.error('登录失败')
            this.loginErrUsrnm = '用户名或密码错误'
            this.loginErrPswd = '用户名或密码错误'
            // if (err.response.data.error === 'wrong captcha') {
            //   this.loginErrCpch = '验证码错误'
            //   this.loginInfo.captcha = ''
            //   this.getCaptcha()
            // } else if (err.response.data.error === 'wrong username or password') {
            //   this.loginErrUsrnm = '用户名或密码错误'
            //   this.loginErrPswd = '用户名或密码错误'
            //   this.loginInfo.password = ''
            //   this.getCaptcha()
            // }
          })
        }
      })
    },
    goSignup () {
      this.$router.push({
        path: '/signup',
        query: {
          redirect: this.redirect
        }
      })
    },
    goToMain () {
      window.location = 'https://thu-ai.net'
    }
  }
}
</script>

<style scoped>
.login-container {
  border-radius: 5px;
  background-clip: padding-box;
  margin: auto;
  padding: 0px;
  padding-bottom: 20px;
  width: 480px;
  height: 280px;
  font-weight: 600;
  font-size: 25px;
}
.login-header {
  text-align: center;
  border-top: none;
  border-left: none;
  border-right: none;
}
.login-main {
  font-weight: 400;
  font-size: 18px;
}
.login-title {
  font-weight: 400;
  font-size: 18px;
  vertical-align: middle;
  line-height: 40px;
}
.login-tip {
  font-weight: 400;
  font-size: 16px;
  color: silver;
  margin-bottom: 0;
  padding: 0;
  line-height: 22px;
}
</style>
