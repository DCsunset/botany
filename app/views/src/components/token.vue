<template>
  <div>
    <v-alert v-if="loading" style="margin-left: auto; margin-right: auto; max-width: 600px" color="primary" outlined>
      登录中，请稍候...
    </v-alert>
    <v-alert :color="error ? 'error' : 'success'" style="margin-left: auto; margin-right: auto; max-width: 600px" outlined v-else>
      <div class="mb-4">
        {{ text }}
      </div>
      <v-btn color=primary v-if="error" @click="goToMain">
        返回THU AI主网站
      </v-btn>
    </v-alert>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loading: true,
      text: '',
      error: false
    }
  },
  mounted () {
    this.loading = true
    this.$axios.post(
      '/token',
      { token: this.$route.query.token }
    ).then(res => {
      const logindata = {
        id: res.data.id,
        handle: res.data.handle,
        privilege: parseInt(res.data.privilege),
        nickname: res.data.nickname
      }
      this.$store.commit('login', logindata)
      this.loading = false
      this.$router.push('/')
      this.text = '登录成功！'
      this.error = false
    }).catch(err => {
      this.loading = false
      console.log(err)
      this.text = '登录失败！'
      this.error = true
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
  },
  methods: {
    goToMain () {
      window.location = 'https://thu-ai.net'
    }
  }
}
</script>
