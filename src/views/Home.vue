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
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import VueHcaptcha from '@hcaptcha/vue-hcaptcha';
export default {
  name: 'Home',
  components: { VueHcaptcha },
  data() {
    return {
      loginStatus: 0,
      displayName: ""
    }
  },
  methods: {
    completedVerify() {
      console.log(this.$route)
    },
    login() {
      
    }
  },
  mounted() {
    if (this.$route.query.first_name) {
      this.$data.displayName = `${this.$route.query.first_name}${this.$route.query.last_name ? ` ${this.$route.query.last_name}` : ''}`
      login()
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