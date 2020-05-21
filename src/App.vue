<template>
  <div id="app">
    <LoginStatus
    authenticationApiUrl="https://api.authentication.maximemoreillon.com/"
    authenticationFrontUrl="https://authentication.maximemoreillon.com/"/>
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
