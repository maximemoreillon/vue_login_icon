<template>
  <div id="app">
    <LoginStatus
      authenticationApiUrl="http://172.16.98.151:30091"
      authenticationFrontUrl="http://172.16.98.151:31091"/>
  </div>
</template>

<script>
import LoginStatus from './components/LoginStatus.vue'

export default {
  name: 'App',
  components: {
    LoginStatus
  }
}
</script>

<style>

</style>
