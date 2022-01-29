<template>
  <div class="home">
    <div v-if="loginStatus !== -1">
      <div class="greetings">{{ displayName }}，你好</div>
      <div v-if="loginStatus === 0">
        <div class="descripction">正在验证登录……</div>
      </div>
      <div v-else-if="loginStatus === 1">
        <div class="descripction">请完成以下人机验证，以加入群组。</div>
        <!--vue-hcaptcha :sitekey="sitekey" @verify="captchaVerify" /-->
        <form class="captchaarea" @submit.prevent="captchaVerify">
          <div class="frc-captcha" :data-sitekey="sitekey"></div>
          <button type="submit" class="btn">我已完成验证，继续</button>
        </form>
      </div>
      <div v-else-if="loginStatus === 2">
        <div class="descripction">已完成验证，欢迎入群！<br>您现在可以正常地关闭这个网页。</div>
      </div>
      <div v-else-if="loginStatus === 3">
        <div class="descripction">请稍等……</div>
      </div>
      <div v-else-if="loginStatus === -2">
        <div class="descripction">服务器返回了一个错误：{{errmsg}}<br>请重新申请加群并完成验证。</div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'
import "friendly-challenge/widget"
export default {
  name: 'Home',
  data() {
    return {
      loginStatus: 0,
      displayName: "",
      errmsg: "",
      sitekey: process.env.VUE_APP_SITEKEY
    }
  },
  methods: {
    async captchaVerify(form) {
      const token = form.target[0].value
      if (!token) return
      this.loginStatus = 3
      try {
        const {chat_id, ...tglogin} = this.$route.query
        await axios.post(`https://${process.env.VUE_APP_API_DOMAIN}/verify-captcha`, { token, tglogin, chat_id })
        this.loginStatus = 2
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
      this.loginStatus = 1
      setTimeout(() => {this.$data.showContinue = true}, 5000)
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
.captchaarea {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}
button.btn {
  margin: 10px;
  font-size: 16px;
  padding: 5px 20px;
  background-color: rgb(27, 141, 235);
  border: 1px solid rgb(5, 83, 148);
  color: rgb(255, 255, 255);
  border-radius: 5px;
}
button.btn:hover {
  background-color: rgb(77, 168, 243);
  border: 1px solid rgb(13, 109, 187);
}
button.btn:disabled {
  background-color: rgba(0, 0, 0, 0.3);
  border: 1px solid rgba(0, 0, 0, 0.3);
}
</style>