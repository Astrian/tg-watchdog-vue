<template>
  <div class="home">
    <div v-if="loginStatus !== -1">
      <div class="greetings">{{ displayName }}，你好</div>
      <div v-if="loginStatus === 0">
        <div class="descripction">正在验证登录……</div>
      </div>
      <div v-else-if="loginStatus === 1">
        <div class="descripction">请完成以下人机验证，以加入群组。</div>
        <vue-hcaptcha sitekey="e71af8ea-9ec8-4f48-a898-95fb0686e4f7" @verify="completedVerify" />
      </div>
      <div v-else-if="loginStatus === -2">
        <div class="descripction">服务器返回了一个错误：{{errmsg}}<br>请重新申请加群并完成验证。</div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import VueHcaptcha from '@hcaptcha/vue-hcaptcha'
import axios from 'axios'
export default {
  name: 'Home',
  components: { VueHcaptcha },
  data() {
    return {
      loginStatus: 0,
      displayName: "",
      errmsg: ""
    }
  },
  methods: {
    completedVerify() {
      console.log(this.$route)
    },
    async login() {
      try {
        await axios.post('http://localhost:3721/verify-login', this.$route.query)
        this.loginStatus = 1
      } catch(e) {
        this.loginStatus = -2
        if (e.response) {
          this.errmsg = e.response.data.message || "未知错误"
        } else {
          this.errmsg = "未知错误"
        }
      }
    }
  },
  mounted() {
    if (this.$route.query.first_name) {
      this.$data.displayName = `${this.$route.query.first_name}${this.$route.query.last_name ? ` ${this.$route.query.last_name}` : ''}`
      this.login()
    } else {
      this.loginStatus = -1
    }
  }
}
</script>

<style>
.greetings {
  font-size: 30px;
  margin: 30px;
}
.descripction {
  margin-bottom: 20px;
}
</style>